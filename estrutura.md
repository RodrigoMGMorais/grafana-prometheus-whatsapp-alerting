
├── docker-compose.yml   # Stack em container (Prometheus + Grafana)<br>
├── prometheus.yml       # Configuração de Jobs e Scrape<br>
└── README.md            # Documentação técnica completa<br>

---

## 🛠️ Tecnologias Utilizadas
Métrica / Coletor: windows_exporter (v0.25+)

Time Series Database: Prometheus

Visualização & Engine de Alertas: Grafana

Gateway de Mensagens: UltraMsg (WhatsApp Webhook API)

Ambiente: Windows / Docker

## 📑 Passo a Passo de Configuração
1. Coleta de Métricas (Windows Exporter)
Faça o download do executável ou instalador MSI no repositório oficial:

*https://github.com/prometheus-community/windows_exporter/releases*

Execute o serviço no host Windows.

Para validar a exposição dos coletores e métricas do sistema operacional, abra o navegador e acesse:

http://localhost:9182/metrics

---
## 2. Configuração de Scrape no Prometheus (prometheus.yml)
Insira o bloco de configuração no seu prometheus.yml (disponível neste repositório) para garantir a coleta contínua a cada 15 segundos:

global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'windows-exporter'
    scrape_interval: 15s
    static_configs:
      - targets: ['host.docker.internal:9182']
        labels:
          environment: 'production'
          os: 'windows'


## Validação dos Targets: Acesse http://localhost:9090/targets e confirme se o job windows-exporter está no estado UP.

**3. Subindo a Stack com Docker Compose**
Caso prefira subir o Prometheus e o Grafana rapidamente em containers:

*docker-compose up -d*

Prometheus UI: http://localhost:9090
Grafana UI: http://localhost:3000 (Login: admin / Senha: admin)

---
4. Ajuste Fino e Correção de Variáveis no Grafana ($server)
Para evitar falhas de renderização como N/A ou No Data no Dashboard do Windows Exporter:

Acesse o dashboard no Grafana e vá em Dashboard settings (ícone de engrenagem) -> Variables.

Clique na variável Server (ou crie uma nova variável do tipo Query):

Name: server

Target Data Source: Prometheus

Query Type: Label values

Label Name: instance

Metric: up

Selection Options: Marque a caixa Include All option.

Clique em Apply no canto inferior direito e salve o Dashboard.

5. Criação da Regra de Alerta de Uso de Disco Crítico
No menu lateral do Grafana, navegue em Alerting -> Alert rules -> New alert rule.

Nome da Regra: Alerta - Uso de Disco Acima de 80%

Query (PromQL): Insira a expressão abaixo para calcular a porcentagem ocupada no volume C::

100 - ((windows_logical_disk_free_bytes{volume="C:"} / windows_logical_disk_size_bytes{volume="C:"}) * 100)

Condição de Disparo (Threshold):
Evaluator: Is Above
Value: 80
Evaluate every: 1m for 1m
---

## 6. Configuração do Webhook WhatsApp (Contact Point)
A integração converte o payload de alertas em requisições POST diretas para a API de envio do UltraMsg.

Acesse o painel do UltraMsg, crie sua instância e pareie o WhatsApp via QR Code.

No Grafana, navegue em Alerting -> Contact points -> Add contact point.

Preencha os campos da seguinte forma:

Name: WhatsApp Celular

Integration: Webhook

URL: https://api.ultramsg.com/<INSTANCE_ID>/messages/chat?token=<TOKEN_API>&to=+55<DDD><NUMERO>&body=🚨+ALERTA+GRAFANA:+Uso+de+Disco+C:+acima+de+80%25

## HTTP Method: POST

Clique no botão Test, selecione Send test notification e confirme o recebimento da mensagem no celular.

Clique em Save contact point.

---
## 7. Roteamento de Notificações (Notification Policies)
Para garantir que todos os alertas gerados sejam canalizados para o WhatsApp:

Acesse Alerting -> Notification configuration (ou Notification policies).

Na árvore de políticas, edite a Default policy (ou adicione uma nova rota por labels).

Altere o Default contact point selecionando WhatsApp Celular.

Clique em Save policy.

| Componente | Teste / Verificação | Resultado Esperado | Status |
| :--- | :--- | :--- | :--- |
| **Windows Exporter** | `http://localhost:9182/metrics` | Exposição das métricas do Windows | ✅ Operacional |
| **Prometheus Target** | `http://localhost:9090/targets` | Target `windows-exporter` no estado `UP` | ✅ Operacional |
| **Grafana Dashboard** | Visualização de CPU/RAM/Disco | Gráficos e dados sendo atualizados em tempo real | ✅ Operacional |
| **Webhook UltraMsg** | `POST` na API do UltraMsg | Envio automático da mensagem para o WhatsApp | ✅ Operacional |


✒️ Autor
Desenvolvido por Rodrigo de Morais

Sistemas, Observabilidade & Arquitetura de Dados


