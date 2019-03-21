---
title: Compreender o por meio de exemplos de preços do Azure Data Factory | Documentos da Microsoft
description: Este artigo explica e demonstra o modelo com exemplos detalhados de preços do Azure Data Factory
documentationcenter: ''
author: shlo
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: 80b1f90ee0d9f5003c39eb6a853a07d2d64ca482
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085342"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Noções básicas sobre preços de fábrica de dados por meio de exemplos

Este artigo explica e demonstra o modelo com exemplos detalhados de preços do Azure Data Factory.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados de AWS S3 para armazenamento de Blobs do Azure por hora

Neste cenário, desejar copiar dados do AWS S3 para o armazenamento de Blobs do Azure numa agenda hora a hora.

Para realizar o cenário, terá de criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a ser copiados do AWS S3.

2. Um conjunto de dados de saída para os dados no armazenamento do Azure.

3. Um acionador de agenda para executar o pipeline a cada hora.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço ligado | 2 de leitura/escrita entidade  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para a criação de conjunto de dados, 2 para referências de serviço ligado) |
| Criar Pipeline | 3 entidades de leitura/gravação (1 para a criação do pipeline, 2 para referências do conjunto de dados) |
| Obter o Pipeline | 1 entidade de leitura/escrita |
| Executar o Pipeline | 2 execuções de Atividades (1 para 1 para execuções de atividades, execução do acionador) |
| Tempo de execução do pressuposto de dados de cópia: = 10 min | 10 \* 4 Runtime de integração do azure (DIU definição predefinida = 4) para obter mais informações sobre unidades de integração de dados e a otimização de desempenho de cópia, consulte [neste artigo](copy-activity-performance.md) |
| Pressuposição de Pipeline do monitor: 1 apenas executar Ocorreu | 2 registos de monitorização run repetida (1 para 1 para a execução da atividade, execução de pipeline) |

**Total de preços de cenário: $0.16811**

- Operações da fábrica de dados = **US $0,0001**
  - Leitura/escrita = 10\*00001 = US $0,0001 [1 R/W = US $ 0,50/50000 = 0,00001]
  - Monitorização = 2\*000005 = US $0,00001 [monitorização 1 = US $ 0,25/50000 = 0.000005]
- Orquestração do pipeline &amp; execução = **US $0.168**
  - Execuções de atividades = 001\*2 = 0.002 [1 executar = US $1/1000 = 0,001]
  - Atividades de movimento de dados = US $0.166 (Prorated durante 10 minutos de tempo de execução. US $ 0,25/hora no Runtime de integração do Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiar dados e transformação com o Azure Databricks, uma vez por hora

Neste cenário, deseja copiar dados do AWS S3 para o armazenamento de Blobs do Azure e transformar os dados com o Azure Databricks um cronograma de hora a hora.

Para realizar o cenário, terá de criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a ser copiados do AWS S3 e um conjunto de dados de saída para os dados no armazenamento do Azure.
2. Uma atividade do Azure Databricks para a transformação de dados.
3. Acionador de agenda de um para executar o pipeline a cada hora.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço ligado | 3 de leitura/escrita entidade  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para a criação de conjunto de dados, 2 para referências de serviço ligado) |
| Criar Pipeline | 3 entidades de leitura/gravação (1 para a criação do pipeline, 2 para referências do conjunto de dados) |
| Obter o Pipeline | 1 entidade de leitura/escrita |
| Executar o Pipeline | 3 execuções de Atividades (1 para 2 para execuções de atividades, execução do acionador) |
| Tempo de execução do pressuposto de dados de cópia: = 10 min | 10 \* 4 Runtime de integração do azure (DIU definição predefinida = 4) para obter mais informações sobre unidades de integração de dados e a otimização de desempenho de cópia, consulte [neste artigo](copy-activity-performance.md) |
| Pressuposição de Pipeline do monitor: 1 apenas executar Ocorreu | 3 registos de monitorização run repetida (1 para 2 para a execução da atividade, execução de pipeline) |
| Executar a atividade do Databricks pressuposição: tempo de execução = 10 min | Execução de atividade do Pipeline externo de 10 minutos |

**Total de preços de cenário: $0.16916**

- Operações da fábrica de dados = **US $0.00012**
  - Leitura/escrita = 11\*00001 = US $0.00011 [1 R/W = US $ 0,50/50000 = 0,00001]
  - Monitorização = 3\*000005 = US $0,00001 [monitorização 1 = US $ 0,25/50000 = 0.000005]
- Orquestração do pipeline &amp; execução = **US $0.16904**
  - Execuções de atividades = 001\*3 = 0,003 [1 executar = US $1/1000 = 0,001]
  - Atividades de movimento de dados = US $0.166 (Prorated durante 10 minutos de tempo de execução. US $ 0,25/hora no Runtime de integração do Azure)
  - Atividade do Pipeline externo = US $0.000041 (Prorated durante 10 minutos de tempo de execução. US $0.00025/hora no Runtime de integração do Azure)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copie os dados e transforme com parâmetros dinâmicos, uma vez por hora

Neste cenário, copiar dados de AWS S3 para armazenamento de Blobs do Azure e a transformação com o Azure Databricks (com parâmetros dinâmicos no script) numa programação de hora em hora.

Para realizar o cenário, terá de criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a ser copiados do AWS S3, um conjunto de dados de saída para os dados no armazenamento do Azure.
2. Uma atividade de pesquisa para passar parâmetros dinamicamente para o script de transformação.
3. Uma atividade do Azure Databricks para a transformação de dados.
4. Acionador de agenda de um para executar o pipeline a cada hora.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço ligado | 3 de leitura/escrita entidade  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para a criação de conjunto de dados, 2 para referências de serviço ligado) |
| Criar Pipeline | 3 entidades de leitura/gravação (1 para a criação do pipeline, 2 para referências do conjunto de dados) |
| Obter o Pipeline | 1 entidade de leitura/escrita |
| Executar o Pipeline | 4 execuções de Atividades (1 para execução, 3 para execuções de atividades do acionador) |
| Tempo de execução do pressuposto de dados de cópia: = 10 min | 10 \* 4 Runtime de integração do azure (DIU definição predefinida = 4) para obter mais informações sobre unidades de integração de dados e a otimização de desempenho de cópia, consulte [neste artigo](copy-activity-performance.md) |
| Pressuposição de Pipeline do monitor: 1 apenas executar Ocorreu | 4 registos de monitorização run repetida (1 para execução de pipeline, 3 para execução de atividade) |
| Executar a atividade de pesquisa pressuposição: tempo de execução = 1 min | execução de atividade do Pipeline de 1 min |
| Executar a atividade do Databricks pressuposição: tempo de execução = 10 min | 10 minutos a execução de atividade do Pipeline externo |

**Total de preços de cenário: $0.17020**

- Operações da fábrica de dados = **US $0.00013**
  - Leitura/escrita = 11\*00001 = US $0.00011 [1 R/W = US $ 0,50/50000 = 0,00001]
  - Monitorização = 4\*000005 = US $0.00002 [monitorização 1 = US $ 0,25/50000 = 0.000005]
- Orquestração do pipeline &amp; execução = **US $0.17007**
  - Execuções de atividades = 001\*4 = 0.004 [1 executar = US $1/1000 = 0,001]
  - Atividades de movimento de dados = US $0.166 (Prorated durante 10 minutos de tempo de execução. US $ 0,25/hora no Runtime de integração do Azure)
  - Atividade do pipeline = US $0.00003 (Prorated para 1 minuto de tempo de execução. US $0.002/hora no Runtime de integração do Azure)
  - Atividade do Pipeline externo = US $0.000041 (Prorated durante 10 minutos de tempo de execução. US $0.00025/hora no Runtime de integração do Azure)

## <a name="next-steps"></a>Passos Seguintes

Agora que compreende os preços do Azure Data Factory, pode começar a utilizar!

- [Criar uma fábrica de dados utilizando a IU do Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Criação no Azure Data Factory](author-visually.md)