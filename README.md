# 🚀 Windows Observability & Real-Time WhatsApp Alerting Stack

Pipeline completo de observabilidade para monitoramento de servidores e hosts Windows. A solução coleta métricas de sistema em tempo real com **Windows Exporter**, armazena e processa no **Prometheus**, exibe dashboards dinâmicos no **Grafana** e dispara alertas automáticos diretamente no celular via **Webhook (WhatsApp API / UltraMsg)**.

---

## 🏗️ Arquitetura do Fluxo

```text
[ Servidor Windows ] ──(Porta 9182/metrics)──> [ Prometheus ] ──(PromQL)──> [ Grafana ] ──(Webhook POST)──> [ UltraMsg / WhatsApp ]
