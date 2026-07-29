
├── docker-compose.yml   # Stack em container (Prometheus + Grafana)<br>
├── prometheus.yml       # Configuração de Jobs e Scrape<br>
└── README.md            # Documentação técnica completa<br>

---
🛠️ Tecnologias UtilizadasMétrica / Coletor: windows_exporter (v0.25+)Time Series Database: PrometheusVisualização & Engine de Alertas: GrafanaGateway de Mensagens: UltraMsg (WhatsApp Webhook API)Ambiente: Windows / Docker📑 Passo a Passo de Configuração1. Coleta de Métricas (Windows Exporter)Faça o download do executável ou instalador MSI no repositório oficial:https://github.com/prometheus-community/windows_exporter/releasesExecute o serviço no host Windows.Para validar a exposição dos coletores e métricas do sistema operacional, abra o navegador e acesse:Plaintexthttp://localhost:9182/metrics
2. Configuração de Scrape no Prometheus (prometheus.yml)Insira o bloco de configuração no seu prometheus.yml (disponível neste repositório) para garantir a coleta contínua a cada 15 segundos:YAMLglobal:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'windows-exporter'
    scrape_interval: 15s
    static_configs:
      - targets: ['host.docker.internal:9182'] # Ou o IP/Hostname da sua máquina Windows
        labels:
          environment: 'production'
          os: 'windows'
Validação dos Targets: Acesse http://localhost:9090/targets e confirme se o job windows-exporter está no estado UP.3. Subindo a Stack com Docker ComposeCaso prefira subir o Prometheus e o Grafana rapidamente em containers:Bashdocker-compose up -d
Prometheus UI: http://localhost:9090Grafana UI: http://localhost:3000 (Login: admin / Senha: admin)4. Ajuste Fino e Correção de Variáveis no Grafana ($server)Para evitar falhas de renderização como N/A ou No Data no Dashboard do Windows Exporter:Acesse o dashboard no Grafana e vá em Dashboard settings (ícone de engrenagem) $\rightarrow$ Variables.Clique na variável Server (ou crie uma nova variável do tipo Query):Name: serverTarget Data Source: PrometheusQuery Type: Label valuesLabel Name: instanceMetric: upSelection Options: Marque a caixa Include All option.Clique em Apply no canto inferior direito e salve o Dashboard.5. Criação da Regra de Alerta de Uso de Disco CríticoNo menu lateral do Grafana, navegue em Alerting $\rightarrow$ Alert rules $\rightarrow$ New alert rule.Nome da Regra: Alerta - Uso de Disco Acima de 80%Query (PromQL): Insira a expressão abaixo para calcular a porcentagem ocupada no volume C::Snippet de código100 - ((windows_logical_disk_free_bytes{volume="C:"} / windows_logical_disk_size_bytes{volume="C:"}) * 100)
Condição de Disparo (Threshold):Evaluator: Is AboveValue: 80Evaluate every: 1m for 1m6. Configuração do Webhook WhatsApp (Contact Point)A integração converte o payload de alertas em requisições POST diretas para a API de envio do UltraMsg.Acesse o painel do UltraMsg, crie sua instância e pareie o WhatsApp via QR Code.No Grafana, navegue em Alerting $\rightarrow$ Contact points $\rightarrow$ Add contact point.Preencha os campos da seguinte forma:Name: WhatsApp CelularIntegration: WebhookURL:Plaintext[https://api.ultramsg.com/](https://api.ultramsg.com/)<INSTANCE_ID>/messages/chat?token=<TOKEN_API>&to=+55<DDD><NUMERO>&body=🚨+ALERTA+GRAFANA:+Uso+de+Disco+C:+acima+de+80%25
HTTP Method: POSTClique no botão Test, selecione Send test notification e confirme o recebimento da mensagem no celular.Clique em Save contact point.7. Roteamento de Notificações (Notification Policies)Para garantir que todos os alertas gerados sejam canalizados para o WhatsApp:Acesse Alerting $\rightarrow$ Notification configuration (ou Notification policies).Na árvore de políticas, edite a Default policy (ou adicione uma nova rota por labels).Altere o Default contact point selecionando WhatsApp Celular.Clique em Save policy.✅ Validação e Testes de ProduçãoComponenteTeste / VerificaçãoResultado EsperadoStatusWindows Exporterhttp://localhost:9182/metricsExposição das métricas do Windows✅ OperationalPrometheus Targethttp://localhost:9090/targetsTarget windows-exporter no estado UP✅ OperationalGrafana DashboardVisualização de CPU/RAM/DiscoGráficos e dados sendo atualizados em tempo real✅ OperationalWebhook UltraMsgPOST na API do UltraMsgEnvio automático da mensagem para o WhatsApp✅ Operational✒️ AutorDesenvolvido por Rodrigo de MoraisSistemas, Observabilidade & Arquitetura de Dados
---

### 2. Arquivo `prometheus.yml`

Crie um novo arquivo no repositório chamado `prometheus.yml` e cole o seguinte conteúdo:

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'windows-exporter'
    scrape_interval: 15s
    static_configs:
      - targets: ['host.docker.internal:9182']
        labels:

---
3. Arquivo docker-compose.yml
Crie um arquivo chamado docker-compose.yml para disponibilizar a infraestrutura pronta:

YAML
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: always
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    extra_hosts:
      - "host.docker.internal:host-gateway"

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    depends_on:
      - prometheus

          environment: 'production'
          os: 'windows'
