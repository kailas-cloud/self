# Valkey Cluster (via Operator)

3-node Valkey кластер с valkey-search модулем и координатором для cross-shard поиска.

## Требования

- Установленный valkey-operator в кластере (repo: `self/shared/valkey/operator`)
- StorageClass `hcloud-volumes` (Hetzner Cloud Volumes)

## Установка оператора

```bash
kubectl apply -k self/shared/valkey/operator/config/default
```

## Деплой кластера

```bash
kubectl apply -k self/shared/valkey/instance
```

## Шаблон инстанса

Основной манифест кластера:
`self/shared/valkey/instance/valkey.yaml`

Namespace для shared-инстанса: `valkey`.

Если нужен отдельный инстанс под приложение — скопируйте папку `instance` и поменяйте `namespace`, `metadata.name` и ресурсы.

## Конфигурация

### Valkey CR (instance manifest)

`self/shared/valkey/instance/valkey.yaml`

| Параметр | Значение | Описание |
|----------|----------|----------|
| `nodes` | 3 | Количество primary нод (shards) |
| `replicas` | 0 | Реплики на shard (0 = только primaries) |
| `image` | `valkey/valkey-bundle:9.0.0-alpine` | Docker образ с модулями |
| `storage` | 10Gi | Размер PVC на каждую ноду |

### Модули

Загружается `valkey-search` с аргументами:
- `--use-coordinator` - включает cross-shard search
- `--reader-threads 8` - потоки для чтения
- `--writer-threads 4` - потоки для записи
- `--hnsw-block-size 10000` - размер блока HNSW индекса

Оператор запускает `valkey-server /valkey/etc/valkey.conf` как entrypoint, поэтому модульные аргументы берутся из конфига и `*_MODULE_ARGS` больше не нужны.

## Проверка

```bash
# Статус Valkey CR
kubectl get valkey -n valkey

# Pods
kubectl get pods -n valkey

# Cluster info
kubectl -n valkey exec valkey-cluster-0 -- valkey-cli CLUSTER INFO

# Loaded modules
kubectl -n valkey exec valkey-cluster-0 -- valkey-cli MODULE LIST

# Cluster nodes
kubectl -n valkey exec valkey-cluster-0 -- valkey-cli CLUSTER NODES
```

## Подключение

### Из кластера

```bash
# Service endpoint
valkey-cluster.valkey.svc.cluster.local:6379

# Отдельные ноды
valkey-cluster-0.valkey-cluster-headless.valkey.svc.cluster.local:6379
valkey-cluster-1.valkey-cluster-headless.valkey.svc.cluster.local:6379
valkey-cluster-2.valkey-cluster-headless.valkey.svc.cluster.local:6379
```

### Port-forward для локального доступа

```bash
kubectl -n valkey port-forward svc/valkey-cluster 6379:6379
```

## Проверка Coordinator

Coordinator обеспечивает синхронизацию FT индексов между всеми нодами кластера.

### Тест из другого namespace

```bash
# Создать тестовый под в redcat namespace
kubectl run valkey-shell -n redcat --image=valkey/valkey-bundle:9.0.0-alpine \
  --command -- sleep infinity

# Получить пароль
PASSWORD=$(kubectl get secret valkey-cluster -n valkey -o jsonpath='{.data.password}' | base64 -d)

# Создать индекс через cluster service
kubectl exec -n redcat valkey-shell -- \
  valkey-cli -h valkey-cluster.valkey.svc.cluster.local -a $PASSWORD \
  FT.CREATE test_idx SCHEMA vec VECTOR HNSW 6 DIM 4 DISTANCE_METRIC COSINE TYPE FLOAT32

# Проверить что индекс появился на всех нодах
for i in 0 1 2; do
  echo "=== Node $i ==="
  kubectl exec -n redcat valkey-shell -- \
    valkey-cli -h valkey-cluster-$i.valkey-cluster-headless.valkey.svc.cluster.local \
    -a $PASSWORD FT._LIST
done
```

### Ожидаемый результат

```
=== Node 0 ===
test_idx
=== Node 1 ===
test_idx
=== Node 2 ===
test_idx
```

✅ Индекс автоматически реплицируется на все ноды кластера.

## Использование Vector Search

**Важно**: valkey-search требует хотя бы один VECTOR field в индексе.

```bash
# Подключение
kubectl -n valkey exec -it valkey-cluster-0 -- valkey-cli -a $PASSWORD

# Создать индекс (распределяется по всем шардам автоматически)
FT.CREATE idx SCHEMA title TAG vector VECTOR HNSW 6 DIM 128 DISTANCE_METRIC COSINE TYPE FLOAT32

# Добавить документ
HSET doc:1 title "Hello World" vector "\x00\x00\x80?\x00\x00\x00@..."

# KNN поиск (работает cross-shard благодаря координатору)
FT.SEARCH idx "*=>[KNN 10 @vector $vec]" PARAMS 2 vec "\x00\x00\x80?..." DIALECT 2
```

## Масштабирование

Изменить количество нод:

```yaml
spec:
  nodes: 5  # Увеличить до 5 shards
```

```bash
kubectl apply -k self/shared/valkey/instance
```

Оператор автоматически:
1. Создаст новые поды
2. Добавит их в кластер (CLUSTER MEET)
3. Перераспределит слоты

## Удаление

```bash
kubectl delete -k self/shared/valkey/instance
```

**Внимание**: PVC не удаляются автоматически! Для полной очистки:

```bash
kubectl delete pvc -n valkey -l app.kubernetes.io/instance=valkey-cluster
```
