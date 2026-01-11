# kailas-cloud/self

Декларативный набор Kubernetes-манифестов (Kustomize + Helm) для поднятия **личного self-hosted AI-стека**.

## 🚀 Сервисы

| Сервис | URL | Описание |
|--------|-----|----------|
| **Open WebUI** | [llm.kailas.cloud](https://llm.kailas.cloud) | Интерфейс для работы с LLM через Ollama |
| **n8n** | [n8n.kailas.cloud](https://n8n.kailas.cloud) | Автоматизация и workflow |
| **Grafana** | [grafana.kailas.cloud](https://grafana.kailas.cloud) | Мониторинг и дашборды |

## 🧠 Основная идея

Основная цель не "чатик", а **дать моделям больше контекста** и жить с ними рядом, а не через чужие API.

---

## 📦 Что внутри

### AI Stack (namespace: `llm`)
- **Ollama** - backend для запуска LLM моделей
- **Open WebUI** - веб-интерфейс с GitHub OAuth
- **n8n** - автоматизация workflows с GitHub OAuth

### Monitoring (namespace: `monitoring`)
- **kube-prometheus-stack** (via Helm):
  - Prometheus Operator
  - Grafana
  - Alertmanager
  - kube-state-metrics
  - node-exporter

---

## 🎯 Зачем это

- Живём в **Hetzner**
- Крутим **k3s-кластер**
- Поддерживаем **личный AI stack**
- Экспериментируем с памятью, контекстом, агентами, интеграциями

Никакого "enterprise", никакого SLA: **Just for fun**
