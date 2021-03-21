---
title: Limites de memória e concência
description: Veja os limites de memória e de concordância atribuídos aos vários níveis de desempenho e classes de recursos para piscina SQL dedicada em Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0ad93fb4a1269b4cca02b114c0427f0c44a31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96455343"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Limites de memória e concuência para piscina SQL dedicada em Azure Synapse Analytics

Ver os limites de memória e de concordância atribuídos aos vários níveis de desempenho e classes de recursos em Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Definições de capacidade de armazém de dados

As tabelas a seguir mostram a capacidade máxima para o armazém de dados em diferentes níveis de desempenho. Para alterar o nível de desempenho, consulte [Scale compute - portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Níveis de Serviço

Os níveis de serviço variam de DW100c a DW30000c.

| Nível de desempenho | Nós de computação | Distribuições por nó computacional | Memória por armazém de dados (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3.000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

O nível máximo de serviço é DW30000c, que tem 60 nós compute e uma distribuição por nó Compute. Por exemplo, um armazém de dados de 600 TB no DW30000c processa aproximadamente 10 TB por nó compute.

## <a name="concurrency-maximums-for-workload-groups"></a>Máximos de concordância para grupos de trabalho

Com a introdução de grupos de [carga de trabalho,](sql-data-warehouse-workload-isolation.md)o conceito de faixas horárias de concordância já não se aplica.  Os recursos por pedido são atribuídos numa base percentual e especificados na definição do grupo de carga de trabalho.  No entanto, mesmo com a remoção das faixas horárias de condência, existem quantidades mínimas de recursos necessários por consultas com base no nível de serviço.  O quadro abaixo definiu a quantidade mínima de recursos necessários por consulta entre os níveis de serviço e a concordância associada que pode ser alcançada.

|Nível de Serviço|Consultas concurrentas máximas|Min % apoiado para REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12.5%|
|DW300c|12|8%|
|DW400c|16|6,25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0.75%|
|DW7500c|128|0.75%|
|DW10000c|128|0.75%|
|DW15000c|128|0.75%|
|DW30000c|128|0.75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Máximos de concordância para classes de recursos

Para garantir que cada consulta tem recursos suficientes para executar de forma eficiente, o Synapse SQL rastreia a utilização dos recursos, atribuindo slots de conuncy a cada consulta. O sistema coloca consultas numa fila baseada em slots de importância e concuência. As consultas esperam na fila até que estejam disponíveis slots de concuência suficientes. [As faixas](sql-data-warehouse-workload-importance.md) horárias de importância e de concordância determinam a priorização da CPU. Para mais informações, consulte [analisar a sua carga de trabalho](analyze-your-workload.md)

**Classes de recursos estáticos**

A tabela a seguir mostra as consultas simultâneas máximas e as ranhuras de concordância para cada [classe de recursos estáticos](resource-classes-for-workload-management.md).  

| Nível de Serviço | Consultas concurrentas máximas | Vagas de concurrency disponíveis | Slots utilizados por staticrc10 | Slots usados por staticrc20 | Slots usados por staticrc30 | Slots usados por staticrc40 | Slots usados por staticrc50 | Slots usados por staticrc60 | Slots usados por staticrc70 | Slots usados por staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classes dinâmicas de recursos**

A tabela a seguir mostra as consultas simultâneas máximas e as faixas horárias de conusciência para cada [classe dinâmica de recursos](resource-classes-for-workload-management.md). As classes dinâmicas de recursos utilizam uma alocação percentual de memória 3-10-22-70 para classes de recursos de pequeno-médio-grande-xl-age em todos os níveis de serviço.

| Nível de Serviço | Consultas concurrentas máximas | Vagas de concurrency disponíveis | Slots usados por smallrc | Slots utilizados por mediumrc | Slots usados por maiores | Slots usados por xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

Quando não há slots de concuncy suficientes para iniciar a execução de consultas, as consultas são filas e executadas com base na importância.  Se houver uma importância equivalente, as consultas são executadas numa base de primeira e primeira saída.  À medida que as consultas terminam e o número de consultas e slots ficam abaixo dos limites, a Azure Synapse Analytics lança consultas em fila.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como aproveitar as classes de recursos para otimizar ainda mais a sua carga de trabalho, reveja os seguintes artigos:

* [Classes de recursos para gestão da carga de trabalho](resource-classes-for-workload-management.md)
* [Analisar a sua carga de trabalho](analyze-your-workload.md)
