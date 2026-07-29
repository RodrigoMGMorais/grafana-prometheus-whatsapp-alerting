Aqui está o código Markdown **único e completo**.

Basta você clicar no botão **"Copiar"** (no canto superior direito do bloco abaixo) e colar direto dentro do arquivo `README.md` no seu GitHub:

```markdown
# 🚀 Windows Observability & Real-Time WhatsApp Alerting Stack

Pipeline de observabilidade de nível enterprise para monitoramento contínuo de servidores e hosts Windows. A arquitetura coleta métricas de sistema em tempo real via **Windows Exporter**, processa e armazena no **Prometheus**, exibe gráficos dinâmicos no **Grafana** e dispara alertas críticos instantâneos diretamente no celular via **Webhook (WhatsApp API / UltraMsg)**.

---

## 🏗️ Arquitetura da Solução

```text
[ Servidor Windows ] ──(Porta 9182/metrics)──>
 [ Prometheus ] ──(PromQL)──>
 [ Grafana ] ──(Webhook POST)──>
 [ UltraMsg / WhatsApp ]

```

---

## 🛠️ Tecnologias Utilizadas

* **Métrica / Coletor:** `windows_exporter` (v0.25+)
* **Time Series Database:** Prometheus
* **Visualização & Engine de Alertas:** Grafana
* **Gateway de Mensagens:** UltraMsg (WhatsApp Webhook API)
* **Ambiente:** Windows / Docker

---

## 📑 Passo a Passo de Configuração

### Passo 1: Coleta de Métricas (Windows Exporter)

1. Baixe o executável oficial do `windows_exporter.exe`.
2. Execute o serviço no host Windows.
3. Valide a exposição das métricas acessando no navegador:
```text
http://localhost:9182/metrics

```



---

### Passo 2: Configuração de Scrape no Prometheus (`prometheus.yml`)

Adicione o job do Windows Exporter ao arquivo de configuração do Prometheus para realizar a coleta contínua:

```yaml
scrape_configs:
  - job_name: 'windows-exporter'
    scrape_interval: 15s
    static_configs:
      - targets: ['host.docker.internal:9182']
        labels:
          environment: 'production'
          os: 'windows'

```

---

### Passo 3: Ajuste Fino da Variável no Grafana (`$server`)

Para evitar erros de carregamento (`N/A` ou `No Data`) nos painéis do Grafana:

1. Acesse **Dashboard settings** $\rightarrow$ **Variables**.
2. Edite a variável **`Server`** (ou crie uma nova do tipo **Query**):
* **Target Data Source:** `Prometheus`
* **Query Type:** `Label values`
* **Label Name:** `instance`
* **Metric:** `up`
* **Selection Options:** Marque a opção `Include All option`.


3. Clique em **Apply** e salve o Dashboard.

---

### Passo 4: Criação da Regra de Alerta de Disco Crítico

1. Navegue até **Alerting** $\rightarrow$ **Alert rules** $\rightarrow$ **New alert rule**.
2. Insira a consulta PromQL para calcular o percentual de uso do volume `C:`:

```promql
100 - ((windows_logical_disk_free_bytes{volume="C:"} / windows_logical_disk_size_bytes{volume="C:"}) * 100)

```

3. Defina a condição de disparo: **Is Above 80** (Alerta para consumo de disco acima de 80%).

---

### Passo 5: Integração do Webhook WhatsApp (Contact Point)

1. Crie uma conta e conecte a sua instância de WhatsApp no serviço **UltraMsg**.
2. No Grafana, navegue em **Alerting** $\rightarrow$ **Contact points** $\rightarrow$ **Add contact point**.
3. Preencha a integração:
* **Name:** `WhatsApp Celular`
* **Integration:** `Webhook`
* **URL:**
```text
[https://api.ultramsg.com/](https://api.ultramsg.com/)<INSTANCE_ID>/messages/chat?token=<TOKEN_API>&to=+55<DDD><NUMERO>&body=🚨+ALERTA+GRAFANA:+Uso+de+Disco+C:+acima+de+80%25

```


* **HTTP Method:** `POST`


4. Clique em **Test**, confirme o recebimento da mensagem no celular e salve o contact point.

---

### Passo 6: Roteamento de Alertas (Notification Policies)

1. Acesse **Alerting** $\rightarrow$ **Notification configuration** (ou **Notification policies**).
2. Edite a **Default policy**.
3. Altere o **Default contact point** para **`WhatsApp Celular`**.
4. Salve as alterações da política.

---

## ✅ Validação e Testes de Produção

| Etapa | Ferramenta | Status |
| --- | --- | --- |
| **Coleta de Métricas** | Windows Exporter | ✅ Ativo e respondendo na porta 9182 |
| **Ingestão de Dados** | Prometheus | ✅ Target com status `UP` |
| **Painel de Controle** | Grafana | ✅ Dashboards de CPU, RAM e Disco operacionais |
| **Notificação de Alertas** | WhatsApp / UltraMsg | ✅ Mensagens críticas de teste e produção entregues via Webhook |

---

# 🚀 Windows Observability & Real-Time WhatsApp Alerting Stack

Pipeline de observabilidade de nível enterprise para monitoramento contínuo de servidores e hosts Windows. A arquitetura coleta métricas de sistema em tempo real via **Windows Exporter**, processa e armazena no **Prometheus**, exibe gráficos dinâmicos no **Grafana** e dispara alertas críticos instantâneos diretamente no celular via **Webhook (WhatsApp API / UltraMsg)**.

---

## 🔗 Links e Recursos Oficiais

* **Windows Exporter (Releases):** [https://github.com/prometheus-community/windows_exporter/releases](https://github.com/prometheus-community/windows_exporter/releases)
* **Prometheus Official Docs:** [https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)
* **Grafana Alerting Docs:** [https://grafana.com/docs/grafana/latest/alerting/](https://grafana.com/docs/grafana/latest/alerting/)
* **UltraMsg (WhatsApp API):** [https://ultramsg.com/](https://ultramsg.com/)
* **Dashboard Template (Grafana Labs #14518):** [https://grafana.com/grafana/dashboards/14518-windows-exporter-dashboard/](https://grafana.com/grafana/dashboards/14518-windows-exporter-dashboard/)

---

## 🏗️ Arquitetura da Solução

```text
[ Servidor Windows ] ──(Porta 9182/metrics)──>
[ Prometheus ] ──(PromQL)──>
[ Grafana ] ──(Webhook POST)──>
[ UltraMsg / WhatsApp ]

windows-observability-stack/
│
├── README.md                 # A documentação completa (que acabamos de criar)
├── prometheus.yml            # O arquivo de configuração do Prometheus
├── docker-compose.yml        # (Opcional) Para subir Prometheus + Grafana com 1 comando
└── dashboards/
    └── windows-exporter.json # O backup/export em JSON do seu Dashboard do Grafana

---

## ✒️ Autor

Desenvolvido por **Rodrigo de Morais**

*Sistemas, Observabilidade & Arquitetura de Dados*

```

```
