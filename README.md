# 🚀 Windows Observability & Real-Time WhatsApp Alerting Stack

Pipeline de observabilidade de nível enterprise para monitoramento contínuo de servidores e hosts Windows. A arquitetura coleta métricas de sistema em tempo real via **Windows Exporter**, processa e armazena no **Prometheus**, exibe gráficos dinâmicos no **Grafana** e dispara alertas críticos instantâneos diretamente no celular via **Webhook (WhatsApp API / UltraMsg)**.

---

## 🔗 Links e Recursos Oficiais

* **Windows Exporter (Releases):** https://github.com/prometheus-community/windows_exporter/releases
* **Prometheus Documentation:** https://prometheus.io/docs/introduction/overview/
* **Grafana Alerting Guide:** https://grafana.com/docs/grafana/latest/alerting/
* **UltraMsg (WhatsApp Gateway):** https://ultramsg.com/
* **Dashboard Template (Grafana Labs #14518):** https://grafana.com/grafana/dashboards/14518-windows-exporter-dashboard/

---

## 🏗️ Arquitetura da Solução

```text
[ Servidor Windows ] ──(Porta 9182/metrics)──>
[ Prometheus ] ──(PromQL)──>
[ Grafana ] ──(Webhook POST)──>
[ UltraMsg / WhatsApp ]

---

