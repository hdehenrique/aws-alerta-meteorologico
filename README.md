---

# 🌩️ aws-alertas-meteorologicos

Sistema de monitoramento climático construído sobre a AWS**, que consome dados da API da Tomorrow.io, gera alertas em tempo real via SMS e e-mail com base em condições meteorológicas críticas, e armazena todos os dados em buckets do Amazon S3 organizados por camadas como raw e gold para posterior análise por times de dados.

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

![image](https://github.com/user-attachments/assets/750f65a5-1899-45e7-94cc-28ea9446735d)

A arquitetura é dividida em duas camadas:

### ⏱ Realtime
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

O sistema verifica, a cada ciclo de coleta, se os dados meteorológicos excedem os seguintes limiares críticos:

| Parâmetro                      | Limite de Alerta | Unidade |
|-------------------------------|------------------|---------|
| Probabilidade de precipitação | ≥ 80             | %       |
| Intensidade da chuva          | ≥ 20             | mm/h    |
| Rajada de vento               | ≥ 22.2           | m/s     |
| Velocidade do vento           | ≥ 13.9           | m/s     |


> - **Rajada de vento:** 22.2 m/s equivale a 80 km/h  
> - **Velocidade do vento:** 13.9 m/s equivale a 50 km/h  
> - **Probabilidade de precipitação:** em porcentagem (%)  
> - **Intensidade da chuva:** em milímetros por hora (mm/h)

---

## ✉️ Alertas Meteorológicos

- **SMS:** resumo objetivo da ocorrência crítica  
- **E-mail:** descrição completa com local, data/hora, variáveis medidas e links para dashboards (opcional)

Ambos os canais são gerenciados via **Amazon SNS**, garantindo escalabilidade e alta entrega.


