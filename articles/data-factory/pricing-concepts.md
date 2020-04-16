---
title: Compreender os preços da Fábrica de Dados Azure através de exemplos
description: Este artigo explica e demonstra o modelo de preços da Fábrica de Dados Azure com exemplos detalhados
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414932"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Compreender os preços da fábrica de dados através de exemplos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explica e demonstra o modelo de preços da Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços utilizados nestes exemplos abaixo são hipotéticos e não se destinam a implicar preços reais.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados da AWS S3 para o armazenamento da Blob Azure de hora em hora

Neste cenário, pretende copiar dados da AWS S3 para o armazenamento da Blob Azure numa programação horária.

Para concretizar o cenário, é necessário criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a copiar a partir de AWS S3.

2. Um conjunto de dados de saída para os dados do Armazenamento Azure.

3. Um gatilho para executar o oleoduto a cada hora.

   ![Cenário1](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar serviço ligado | 2 Entidade de Leitura/Escrita  |
| Criar Conjuntos de Dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviço seletos) |
| Criar pipeline | 3 Entidades de Leitura/Escrita (1 para criação de gasodutos, 2 para referências de conjunto de dados) |
| Obter Pipeline | 1 Entidade de Leitura/Escrita |
| Gasoduto de execução | 2 Funcionações de atividade (1 para execução do gatilho, 1 para execução de atividade) |
| Pressuposto de Dados de Cópia: tempo de execução = 10 min | 10 \* 4 Tempo de execução de integração azure (definição padrão de DIU = 4) Para mais informações sobre unidades de integração de dados e otimização do desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Pressuposto do Gasoduto monitor: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização reexperimentados (1 para execução de gasodutos, 1 para execução de atividade) |

**Preço total do cenário: $0.16811**

- Operações de fábrica de dados = **$0.0001**
  - Ler/Escrever =\*10 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização =\*2 000005 = $0,00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução &amp; da Orquestração do Oleoduto = **$0.168**
  - Corridas de Atividade\*= 001 2 = 0,002 [1 corrida = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prorated por 10 minutos de tempo de execução. $0.25/hora no Tempo de Execução de Integração Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiar dados e transformar-se com tijolos de dados Azure de hora em hora

Neste cenário, pretende copiar dados da AWS S3 para o armazenamento da Blob Azure e transformar os dados com os Bricks Azure numa programação horária.

Para concretizar o cenário, é necessário criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a copiar a partir de AWS S3, e um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade de Azure Databricks para a transformação de dados.
3. Um gatilho para executar o oleoduto a cada hora.

![Cenário2](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar serviço ligado | 3 Entidade de Leitura/Escrita  |
| Criar Conjuntos de Dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviço seletos) |
| Criar pipeline | 3 Entidades de Leitura/Escrita (1 para criação de gasodutos, 2 para referências de conjunto de dados) |
| Obter Pipeline | 1 Entidade de Leitura/Escrita |
| Gasoduto de execução | 3 Funcionações de atividade (1 para execução do gatilho, 2 para execução de atividade) |
| Pressuposto de Dados de Cópia: tempo de execução = 10 min | 10 \* 4 Tempo de execução de integração azure (definição padrão de DIU = 4) Para mais informações sobre unidades de integração de dados e otimização do desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Pressuposto do Gasoduto monitor: Apenas 1 corrida ocorreu | 3 Registos de execução de monitorização reexperimentados (1 para execução de gasodutos, 2 para execução de atividade) |
| Executar atividade de Databricks Assunção: tempo de execução = 10 min | Execução de atividade externa de gasoduto de 10 min |

**Preço total do cenário: $0.16916**

- Operações de fábrica de dados = **$0.00012**
  - Ler/Escrever =\*11 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização =\*3 000005 = $0.00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução &amp; da Orquestração do Oleoduto = **$0.16904**
  - Corridas de Atividade\*= 001 3 = 0,003 [1 corrida = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prorated por 10 minutos de tempo de execução. $0.25/hora no Tempo de Execução de Integração Azure)
  - Atividade externa do gasoduto = $0.000041 (Prorated por 10 minutos de tempo de execução. $0.00025/hora no Tempo de Integração Azure)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copiar dados e transformar com parâmetros dinâmicos de hora em hora

Neste cenário, pretende copiar dados da AWS S3 para o armazenamento da Blob Azure e transformar-se com os Bricks Azure (com parâmetros dinâmicos no script) numa programação horária.

Para concretizar o cenário, é necessário criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a copiar a partir de AWS S3, um conjunto de dados de saída para os dados sobre o armazenamento do Azure.
2. Uma atividade de procura ção para passar parâmetros dinamicamente para o roteiro de transformação.
3. Uma atividade de Azure Databricks para a transformação de dados.
4. Um gatilho para executar o oleoduto a cada hora.

![Cenário3](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar serviço ligado | 3 Entidade de Leitura/Escrita  |
| Criar Conjuntos de Dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviço seletos) |
| Criar pipeline | 3 Entidades de Leitura/Escrita (1 para criação de gasodutos, 2 para referências de conjunto de dados) |
| Obter Pipeline | 1 Entidade de Leitura/Escrita |
| Gasoduto de execução | 4 Funcionações de atividade (1 para execução de gatilho, 3 para execução de atividade) |
| Pressuposto de Dados de Cópia: tempo de execução = 10 min | 10 \* 4 Tempo de execução de integração azure (definição padrão de DIU = 4) Para mais informações sobre unidades de integração de dados e otimização do desempenho da cópia, consulte [este artigo](copy-activity-performance.md) |
| Pressuposto do Gasoduto monitor: Apenas 1 corrida ocorreu | 4 Registos de execução de monitorização reexperimentados (1 para execução de gasodutos, 3 para execução de atividade) |
| Executar atividade de procuração Assunção: tempo de execução = 1 min | Execução da atividade do gasoduto de 1 min |
| Executar atividade de Databricks Assunção: tempo de execução = 10 min | Execução de atividade externa de gasoduto de 10 min |

**Preço total do cenário: $0.17020**

- Operações de fábrica de dados = **$0.00013**
  - Ler/Escrever =\*11 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização =\*4 000005 = $0.00002 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução &amp; da Orquestração do Oleoduto = **$0.17007**
  - Corridas de Atividade\*= 001 4 = 0,004 [1 corrida = $1/1000 = 0,001]
  - Atividades de Movimento de Dados = $0.166 (Prorated por 10 minutos de tempo de execução. $0.25/hora no Tempo de Execução de Integração Azure)
  - Atividade do Gasoduto = $0.00003 (Prorated por 1 minuto de tempo de execução. $0.002/hora no Tempo de Execução de Integração Azure)
  - Atividade externa do gasoduto = $0.000041 (Prorated por 10 minutos de tempo de execução. $0.00025/hora no Tempo de Integração Azure)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando o depuramento do fluxo de dados de mapeamento para um dia normal de trabalho

Como Engenheiro de Dados, é responsável por conceber, construir e testar fluxos de dados de mapeamento todos os dias. Inicia sessão na UI ADF de manhã e ativa o modo Debug para fluxos de dados. O TTL padrão para sessões de Debug é de 60 minutos. Trabalhas durante todo o dia durante 8 horas, para que a tua sessão de Debug nunca expire. Portanto, a sua carga para o dia será:

**8 (horas) x 8 (núcleos otimizados com cálculo) x $0,193 = $12,35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transforme dados em loja blob com fluxos de dados de mapeamento

Neste cenário, pretende transformar dados na Blob Store visualmente em fluxos de dados de mapeamento ADF numa programação horária.

Para concretizar o cenário, é necessário criar um pipeline com os seguintes itens:

1. Uma atividade de Fluxo de Dados com a lógica de transformação.

2. Um conjunto de dados de entrada para os dados sobre o Armazenamento Do Azure.

3. Um conjunto de dados de saída para os dados do Armazenamento Azure.

4. Um gatilho para executar o oleoduto a cada hora.

| **Operações** | **Tipos e Unidades** |
| --- | --- |
| Criar serviço ligado | 2 Entidade de Leitura/Escrita  |
| Criar Conjuntos de Dados | 4 Entidades de leitura/escrita (2 para criação de conjuntos de dados, 2 para referências de serviço seletos) |
| Criar pipeline | 3 Entidades de Leitura/Escrita (1 para criação de gasodutos, 2 para referências de conjunto de dados) |
| Obter Pipeline | 1 Entidade de Leitura/Escrita |
| Gasoduto de execução | 2 Funcionações de atividade (1 para execução do gatilho, 1 para execução de atividade) |
| Pressupostos do Fluxo de Dados: tempo de execução = 10 min + 10 min TTL | 10 \* 16 núcleos de Computação Geral com TTL de 10 |
| Pressuposto do Gasoduto monitor: Apenas 1 corrida ocorreu | 2 Registos de execução de monitorização reexperimentados (1 para execução de gasodutos, 1 para execução de atividade) |

**Preço total do cenário: $1.4631**

- Operações de fábrica de dados = **$0.0001**
  - Ler/Escrever =\*10 00001 = $0.0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorização =\*2 000005 = $0,00001 [1 Monitorização = $0,25/50000 = 0,000005]
- Execução &amp; da Orquestração do Oleoduto = **$1.463**
  - Corridas de Atividade\*= 001 2 = 0,002 [1 corrida = $1/1000 = 0,001]
  - Atividades de Fluxo de Dados = $1.461 proclamado por 20 minutos (tempo de execução de 10 minutos + 10 minutos TTL). $0.274/hora no Azure Integration Runtime com 16 núcleos de cálculo geral

## <a name="next-steps"></a>Passos seguintes

Agora que compreende os preços da Azure Data Factory, pode começar!

- [Criar uma fábrica de dados utilizando a Azure Data Factory UI](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Autoria visual na Azure Data Factory](author-visually.md)
