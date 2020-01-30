---
title: Noções básicas sobre preços Azure Data Factory por meio de exemplos
description: Este artigo explica e demonstra o modelo de preços de Azure Data Factory com exemplos detalhados
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: ee5acc97e4b05a0e93f4ceee8c04b400da211b49
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769499"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Noções básicas sobre preços Data Factory por meio de exemplos

Este artigo explica e demonstra o modelo de preços de Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços usados nestes exemplos abaixo são hipotéticos e não têm o objetivo de implicar no preço real.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados do AWS S3 para o armazenamento de BLOBs do Azure por hora

Nesse cenário, você deseja copiar dados do AWS S3 para o armazenamento de BLOBs do Azure em um agendamento por hora.

Para realizar o cenário, você precisa criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para o dado a ser copiado do AWS S3.

2. Um DataSet de saída para os dados no armazenamento do Azure.

3. Um gatilho de agendamento para executar o pipeline a cada hora.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidade de leitura/gravação  |
| Criar conjuntos de os | 4 entidades de leitura/gravação (2 para criação de conjunto de conjuntos, 2 para referências de serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências de DataSet) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 execuções de atividade (1 para execução de gatilho, 1 para execuções de atividade) |
| Pressuposição de Copiar Dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime (configuração padrão de DIU = 4) para obter mais informações sobre unidades de integração de dados e otimizar o desempenho de cópia, consulte [Este artigo](copy-activity-performance.md) |
| Monitorar a suposição de pipeline: apenas 1 execução ocorreu | 2 registros de execução de monitoramento repetidos (1 para execução de pipeline, 1 para execução de atividade) |

**Preço do cenário total: $0.16811**

- Operações de Data Factory = **$0.01**
  - Ler/Escrever = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2\*000005 = $0,00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução de &amp; de orquestração de pipeline = **$0.168**
  - Corridas de Atividade = 001\*2 = 0,002 [1 corrida = $1/1000 = 0,001]
  - Atividades de movimentação de dados = $0.166 (rateado por 10 minutos de tempo de execução. US $0,25/hora no Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiar dados e transformar com Azure Databricks por hora

Nesse cenário, você deseja copiar dados do AWS S3 para o armazenamento de BLOBs do Azure e transformar os dados com Azure Databricks em uma agenda por hora.

Para realizar o cenário, você precisa criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para o data a ser copiado do AWS S3, e um conjunto de dados de saída para o armazenamento do Azure.
2. Uma Azure Databricks atividade para a transformação de dados.
3. Um gatilho de agendamento para executar o pipeline a cada hora.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidade de leitura/gravação  |
| Criar conjuntos de os | 4 entidades de leitura/gravação (2 para criação de conjunto de conjuntos, 2 para referências de serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências de DataSet) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 3 execuções de atividade (1 para execução de gatilho, 2 para execuções de atividade) |
| Pressuposição de Copiar Dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime (configuração padrão de DIU = 4) para obter mais informações sobre unidades de integração de dados e otimizar o desempenho de cópia, consulte [Este artigo](copy-activity-performance.md) |
| Monitorar a suposição de pipeline: apenas 1 execução ocorreu | 3 registros de execução de monitoramento repetidos (1 para execução de pipeline, 2 para execução de atividade) |
| Executar pressuposição de atividade do databricks: tempo de execução = 10 min | 10 minutos de execução de atividade de pipeline externo |

**Preço do cenário total: $0.16916**

- Operações de Data Factory = **$0.12**
  - Ler/Escrever = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 3\*000005 = $0,00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução de &amp; de orquestração de pipeline = **$0.16904**
  - Corridas de Atividade = 001\*3 = 0,003 [1 corrida = $1/1000 = 0,001]
  - Atividades de movimentação de dados = $0.166 (rateado por 10 minutos de tempo de execução. US $0,25/hora no Azure Integration Runtime)
  - Atividade de pipeline externo = $0.41 (rateado por 10 minutos de tempo de execução. $0.00025/hora em Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copiar dados e transformar com parâmetros dinâmicos por hora

Nesse cenário, você deseja copiar dados do AWS S3 para o armazenamento de BLOBs do Azure e transformar com Azure Databricks (com parâmetros dinâmicos no script) em uma agenda por hora.

Para realizar o cenário, você precisa criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para o dado a ser copiado do AWS S3, um conjunto de dados de saída para o armazenamento do Azure.
2. Uma atividade de pesquisa para passar parâmetros dinamicamente para o script de transformação.
3. Uma Azure Databricks atividade para a transformação de dados.
4. Um gatilho de agendamento para executar o pipeline a cada hora.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidade de leitura/gravação  |
| Criar conjuntos de os | 4 entidades de leitura/gravação (2 para criação de conjunto de conjuntos, 2 para referências de serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências de DataSet) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 4 execuções de atividade (1 para execução de gatilho, 3 para execuções de atividade) |
| Pressuposição de Copiar Dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime (configuração padrão de DIU = 4) para obter mais informações sobre unidades de integração de dados e otimizar o desempenho de cópia, consulte [Este artigo](copy-activity-performance.md) |
| Monitorar a suposição de pipeline: apenas 1 execução ocorreu | 4 registros de execução de monitoramento repetidos (1 para execução de pipeline, 3 para execução de atividade) |
| Executar pressuposição de atividade de pesquisa: tempo de execução = 1 min | uma execução de atividade de pipeline mín. |
| Executar pressuposição de atividade do databricks: tempo de execução = 10 min | 10 minutos de execução de atividade de pipeline externo |

**Preço do cenário total: $0.17020**

- Operações de Data Factory = **$0.13**
  - Ler/Escrever = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 4\*000005 = $0,00002 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução de &amp; de orquestração de pipeline = **$0.17007**
  - Corridas de Atividade = 001\*4 = 0,004 [1 corrida = $1/1000 = 0,001]
  - Atividades de movimentação de dados = $0.166 (rateado por 10 minutos de tempo de execução. US $0,25/hora no Azure Integration Runtime)
  - Atividade de pipeline = $0.03 (rateado por 1 minuto de tempo de execução. $0,002/hora em Azure Integration Runtime)
  - Atividade de pipeline externo = $0.41 (rateado por 10 minutos de tempo de execução. $0.00025/hora em Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando a depuração de fluxo de dados de mapeamento para um workday normal

Como engenheiro de dados, você é responsável por criar, criar e testar fluxos de dados de mapeamento todos os dias. Faça logon na interface do usuário do ADF na manhã e habilite o modo de depuração para fluxos de dados. O TTL padrão para sessões de depuração é de 60 minutos. Você trabalha ao longo do dia por 8 horas, portanto, sua sessão de depuração nunca expira. Portanto, sua cobrança pelo dia será:

**8 (horas) x 8 (núcleos com otimização de computação) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformar dados no repositório de blob com fluxos de dados de mapeamento

Nesse cenário, você deseja transformar os dados no repositório de blob visualmente nos fluxos de dados de mapeamento do ADF em uma agenda por hora.

Para realizar o cenário, você precisa criar um pipeline com os seguintes itens:

1. Uma atividade de fluxo de dados com a lógica de transformação.

2. Um DataSet de entrada para os dados no armazenamento do Azure.

3. Um DataSet de saída para os dados no armazenamento do Azure.

4. Um gatilho de agendamento para executar o pipeline a cada hora.

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidade de leitura/gravação  |
| Criar conjuntos de os | 4 entidades de leitura/gravação (2 para criação de conjunto de conjuntos, 2 para referências de serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências de DataSet) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 execuções de atividade (1 para execução de gatilho, 1 para execuções de atividade) |
| Pressuposições de fluxo de dados: tempo de execução = 10 min + 10 min TTL | 10 \* 16 núcleos de computação geral com TTL de 10 |
| Monitorar a suposição de pipeline: apenas 1 execução ocorreu | 2 registros de execução de monitoramento repetidos (1 para execução de pipeline, 1 para execução de atividade) |

**Preço total do cenário: $1.4631**

- Operações de Data Factory = **$0.01**
  - Ler/Escrever = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização = 2\*000005 = $0,00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução de &amp; de orquestração de pipeline = **$1.463**
  - Corridas de Atividade = 001\*2 = 0,002 [1 corrida = $1/1000 = 0,001]
  - Atividades de fluxo de dados = $1.461 rateado por 20 minutos (tempo de execução de 10 min + 10 minutos TTL). $0.274/hora em Azure Integration Runtime com 16 núcleos de computação geral

## <a name="next-steps"></a>Passos seguintes

Agora que você entendeu os preços de Azure Data Factory, você pode começar!

- [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Criação visual no Azure Data Factory](author-visually.md)
