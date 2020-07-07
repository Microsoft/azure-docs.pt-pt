---
title: Compreender os preços da Azure Data Factory através de exemplos
description: Este artigo explica e demonstra o modelo de preços da Azure Data Factory com exemplos detalhados
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414932"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Compreender os preços do Data Factory através de exemplos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explica e demonstra o modelo de preços da Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não se destinam a implicar preços reais.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados de AWS S3 para Azure Blob armazenamento por hora

Neste cenário, pretende copiar dados do armazenamento AWS S3 para Azure Blob num horário de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para que os dados sejam copiados da AWS S3.

2. Um conjunto de dados de saída para os dados do Azure Storage.

3. Um gatilho de horário para executar o oleoduto a cada hora.

   ![Cenário1](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 2 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 2 A atividade é executada (1 para o gatilho, 1 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização novamente julgados (1 para o gasoduto, 1 para execução de atividade) |

**Preço total do cenário: $0.16811**

- Operações de Fábrica de Dados = **$0.0001**
  - Ler/escrever = 10 \* 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.168**
  - Operações Execuções = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copie os dados e transforme-se com a Azure Databricks por hora

Neste cenário, pretende copiar dados de armazenamento AWS S3 para Azure Blob e transformar os dados com Azure Databricks num horário de hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados a partir de AWS S3, e um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade da Azure Databricks para a transformação de dados.
3. Um gatilho de horário para executar o oleoduto a cada hora.

![Cenário2](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 3 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 3 Funciona a atividade (1 para o gatilho, 2 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 3 Registos de execução de monitorização novamente julgados (1 para o gasoduto, 2 para execução de atividade) |
| Executar atividade databricks Suposição: tempo de execução = 10 min | Execução da atividade do gasoduto externo de 10 min |

**Preço total do cenário: $0.16916**

- Operações de Fábrica de Dados = **$0.00012**
  - Ler/escrever = 11 \* 00001 = $0.00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 3 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.16904**
  - Operações Execuções = 001 \* 3 = 0,003 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)
  - Atividade do Gasoduto Externo = $0.000041 (Prostado para 10 minutos de tempo de execução. $0.00025/hora no Tempo de Execução da Integração Azure)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copie os dados e transforme-os com parâmetros dinâmicos por hora

Neste cenário, pretende copiar dados de armazenamento AWS S3 para Azure Blob e transformar-se com Azure Databricks (com parâmetros dinâmicos no script) numa programação de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados a partir de AWS S3, um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade de procura para passar parâmetros dinamicamente para o script de transformação.
3. Uma atividade da Azure Databricks para a transformação de dados.
4. Um gatilho de horário para executar o oleoduto a cada hora.

![Cenário 3](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 3 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 4 Operações executadas (1 para o gatilho, 3 para execuções de atividade) |
| Copiar Dados Pressuposto: tempo de execução = 10 min | 10 \* 4 Tempo de execução da integração Azure (definição padrão de DI = 4) Para obter mais informações sobre unidades de integração de dados e otimizar o desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 4 Registos de execução de monitorização novamente julgados (1 para o gasoduto, 3 para execução de atividade) |
| Executar Atividade de procuração Suposição: tempo de execução = 1 min | Execução de atividade de 1 min pipeline |
| Executar atividade databricks Suposição: tempo de execução = 10 min | Execução da atividade do gasoduto externo de 10 min |

**Preço total do cenário: $0.17020**

- Operações de Fábrica de Dados = **$0.00013**
  - Ler/escrever = 11 \* 00001 = $0.00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 4 \* 000005 = $0.00002 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$0.17007**
  - Operações Executa = 001 \* 4 = 0,004 [1 run = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prostado para 10 minutos de tempo de execução. $0,25/hora no tempo de funcionamento da integração Azure)
  - Atividade do gasoduto = $0.00003 (Prostado por 1 minuto de tempo de execução. $0.002/hora no tempo de funcionamento da integração Azure)
  - Atividade do Gasoduto Externo = $0.000041 (Prostado para 10 minutos de tempo de execução. $0.00025/hora no Tempo de Execução da Integração Azure)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando o fluxo de dados de mapeamento depurado para um dia de trabalho normal

Como Engenheiro de Dados, é responsável por conceber, construir e testar fluxos de dados de mapeamento todos os dias. Inicie sessão no UI ADF de manhã e ative o modo Debug para Fluxos de Dados. O TTL predefinido para sessões de Debug é de 60 minutos. Trabalhas durante 8 horas durante o dia, para que a tua sessão de Debug nunca expire. Portanto, a sua cobrança para o dia será:

**8 (horas) x 8 (núcleos otimizados por computação) x $0,193 = $12,35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transforme os dados na loja blob com fluxos de dados de mapeamento

Neste cenário, pretende transformar os dados na Blob Store visualmente em fluxos de dados de mapeamento ADF num horário de hora a hora.

Para realizar o cenário, é necessário criar um oleoduto com os seguintes itens:

1. Uma atividade de Fluxo de Dados com a lógica de transformação.

2. Um conjunto de dados de entrada para os dados do Azure Storage.

3. Um conjunto de dados de saída para os dados do Azure Storage.

4. Um gatilho de horário para executar o oleoduto a cada hora.

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar Serviço Linked | 2 Entidade de leitura/escrita  |
| Criar conjuntos de dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviços ligados) |
| Criar Pipeline | 3 Entidades de leitura/escrita (1 para criação de gasodutos, 2 para referências de conjuntos de dados) |
| Obter Pipeline | 1 Entidade de leitura/escrita |
| Executar Pipeline | 2 A atividade é executada (1 para o gatilho, 1 para execuções de atividade) |
| Pressupostos do fluxo de dados: tempo de execução = 10 min + 10 min TTL | 10 \* 16 núcleos de Computação Geral com TTL de 10 |
| Monitor Pipeline Assumption: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização novamente julgados (1 para o gasoduto, 1 para execução de atividade) |

**Preço total do cenário: $1.4631**

- Operações de Fábrica de Dados = **$0.0001**
  - Ler/escrever = 10 \* 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2 \* 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução da Orquestração do Oleoduto &amp; = **$1.463**
  - Operações Execuções = 001 \* 2 = 0,002 [1 run = $1/1000 = 0,001]
  - Atividades de fluxo de dados = $1.461 prociado por 20 minutos (tempo de execução de 10 minutos + 10 minutos TTL). $0.274/hora no Tempo de Execução da Integração Azure com 16 núcleos de cálculo geral

## <a name="next-steps"></a>Passos seguintes

Agora que entende os preços da Azure Data Factory, pode começar!

- [Criar uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Autoria visual na Azure Data Factory](author-visually.md)
