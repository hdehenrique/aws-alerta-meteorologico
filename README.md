
# 🌩️ aws-alertas-meteorologicos

Sistema de monitoramento climático baseado em AWS que consome dados da [Tomorrow.io API](https://app.tomorrow.io/home), gera alertas em tempo real via SMS e e-mail com base em condições meteorológicas críticas, e armazena todos os dados em buckets do Amazon S3 organizados por camadas — como `raw`, `processed` e `gold` — para posterior análise por times de dados.

---

## 🛰️ Visão Geral

Este projeto realiza coletas periódicas de dados meteorológicos (por padrão, a cada 5 minutos), verifica condições críticas (como precipitação intensa, ventos fortes, índice UV elevado etc.) e envia alertas automáticos em tempo real por canais confiáveis.

Independentemente de haver alerta, os dados coletados são armazenados na camada *batch* para posterior análise por times de dados.

---

## 📦 Funcionalidades

- ✅ Consulta da API Tomorrow.io em tempo real e por lote
- ✅ Envio de **SMS e e-mails automáticos** via Amazon SNS
- ✅ Armazenamento e organização em camadas: `raw`, `processed` e `gold`
- ✅ Processamento com AWS Glue + Glue Crawlers + Glue Data Catalog
- ✅ Canal de ingestão em tempo real com **Amazon Kinesis Data Stream**
- ✅ Arquitetura escalável com serviços 100% gerenciados pela AWS

---

## 🧭 Arquitetura

![Arquitetura do Projeto](docs/arquitetura.png)

A arquitetura é dividida em duas camadas:

### ⏱ Tempo Real
- Consulta da Tomorrow.io a cada *X* minutos (configurável)
- Avaliação das condições meteorológicas críticas
- Disparo de **alertas** via SMS e e-mail com Amazon SNS
- Armazenamento simultâneo dos dados em S3 via Lambda e/ou Kinesis

### 🗄️ Batch
- Dados recebidos são salvos em `S3/raw` e mapeados com **Glue Crawler**
- Transformações para `processed` e `gold` com AWS Glue
- Dados disponíveis para análise via **Glue Data Catalog**, Athena ou Lake Formation

---

## ⚙️ Tecnologias e Serviços Utilizados

| Serviço                     | Descrição                                                                 |
|----------------------------|---------------------------------------------------------------------------|
| **AWS Lambda**             | Execução dos scripts de ingestão e envio de alertas                       |
| **Amazon SNS**             | Envio de notificações por SMS e e-mail                                   |
| **Amazon S3**              | Armazenamento de dados por camadas                                        |
| **Amazon EventBridge**     | Agendamento da coleta em tempo real                                       |
| **Amazon Kinesis**         | Streaming de dados meteorológicos em tempo real                          |
| **AWS Glue Crawler**       | Mapeamento e catalogação automática dos dados armazenados                 |
| **AWS Glue Data Catalog**  | Catálogo unificado de metadados para consultas e análises                 |
| **Tomorrow.io API**        | Fonte de dados meteorológicos em tempo real e previsão                    |

---

## 🚨 Regras de Alerta

As regras estão definidas em `monitoramento_config.yaml`, por exemplo:

```yaml
precipitationProbability: { threshold: 20 }  # mm/h
windSpeed: { threshold: 50 }                # km/h
windGust: { threshold: 80 }                 # km/h
uvIndex: { threshold: 6 }
visibility: { threshold: 1 }                # km
temperature:
  max: 35
  min: -10
```

---

## ✉️ Alertas Meteorológicos

- **SMS:** resumo objetivo da ocorrência crítica  
- **E-mail:** descrição completa com local, data/hora, variáveis medidas e links para dashboards (opcional)

Ambos os canais são gerenciados via **Amazon SNS**, garantindo escalabilidade e alta entrega.

---

## 📁 Estrutura do Projeto

```
aws-alertas-Meteorológicos/
│
├── monitoramento/
│   ├── ingestion/
│   ├── alerta/
│   ├── monitoramento_config.yaml
│   └── parseador_campos.py
│
├── batch/
│   ├── ingestao/
│   ├── processamento/
│   └── exportacao/
│
├── infra/                      # IaC: Terraform/CDK
├── tests/                      # Testes unitários
├── docs/
│   └── arquitetura.png
│
├── .gitignore
├── requirements.txt
└── README.md
```

---

## 🚀 Como Executar

1. Clone o projeto:  
```bash
git clone https://github.com/seu-usuario/aws-alertas-Meteorologicos.git
```

2. Instale as dependências (ex: Python):  
```bash
pip install -r requirements.txt
```

3. Crie um `.env` com suas variáveis:
```
API_KEY_TOMORROWIO=xxxxxx
SNS_TOPIC_ARN_EMAIL=arn:aws:sns:us-east-1:xxx:meu-topico-email
SNS_TOPIC_ARN_SMS=arn:aws:sns:us-east-1:xxx:meu-topico-sms
```

4. Suba a infraestrutura com CDK ou Terraform

5. Agende a execução via EventBridge
