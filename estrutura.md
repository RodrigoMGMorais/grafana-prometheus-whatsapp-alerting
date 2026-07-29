
.<br>
├── docker-compose.yml   # Stack em container (Prometheus + Grafana)<br>
├── prometheus.yml       # Configuração de Jobs e Scrape<br>
└── README.md            # Documentação técnica completa<br>

---

🛠️ ## Tecnologias Utilizadas
Métrica / Coletor: windows_exporter (v0.25+)

Time Series Database: Prometheus

Visualização & Engine de Alertas: Grafana

Gateway de Mensagens: UltraMsg (WhatsApp Webhook API)

Ambiente: Windows / Docker

📑 Passo a Passo de Configuração
1. Coleta de Métricas (Windows Exporter)
Faça o download do executável ou instalador MSI no repositório oficial:

https://github.com/prometheus-community/windows_exporter/releases

Execute o serviço no host Windows.

Para validar a exposição dos coletores e métricas do sistema operacional, abra o navegador e acesse:

http://localhost:9182/metrics
