# 🚀 Windows Observability & Self-Healing Alerting Stack

> **Pipeline de Observabilidade Enterprise:** Monitoramento de infraestrutura Windows utilizando **Prometheus**, **Grafana** e automação de alertas em tempo real via **Webhook (WhatsApp API)**.

---

## 📋 Visão Geral da Arquitetura

A solução coleta métricas de sistema em tempo real de servidores/hosts Windows, armazena no Prometheus, constrói dashboards dinâmicos no Grafana e aciona alertas críticos diretamente no celular via WhatsApp.

[ Windows Host ] ──(9182/metrics)──> [ Prometheus ] ──(Query PromQL)──> [ Grafana ] ──(Webhook POST)──> [ UltraMsg / WhatsApp ]

---

## 🛠️ Tecnologias Utilizadas

* **Métrica / Agent:** `windows_exporter` v0.25+
* **Time Series Database:** Prometheus
* **Visualização & Alerting:** Grafana v10+
* **Messaging API:** UltraMsg (WhatsApp Gateway / Webhook)
* **Ambiente:** Docker / Bare-Metal Windows

---

## ⚙️ Passo a Passo de Configuração

### 1. Coleta de Métricas (Windows Exporter)

1. Baixe e instale o `windows_exporter.exe` no servidor Windows.
2. Certifique-se de que o serviço está rodando e expondo as métricas na porta padrão:
   ```text
   http://localhost:9182/metrics

   
