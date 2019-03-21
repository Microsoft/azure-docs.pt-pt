---
title: Limites de memória e simultaneidade - Azure SQL Data Warehouse | Documentos da Microsoft
description: Ver os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 141112b8b6b44706a750d8a97780e018d96a5006
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890802"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limites de memória e simultaneidade para o Azure SQL Data Warehouse
Ver os limites de memória e simultaneidade alocados para os vários níveis de desempenho e classes de recursos no Azure SQL Data Warehouse. Para obter mais informações e para aplicar estas capacidades ao seu plano de gestão da carga de trabalho, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md). 

Existem atualmente duas gerações disponíveis com o SQL Data Warehouse – Gen1 e Gen2. Recomendamos que tire partido de geração 2 do SQL Data Warehouse para obter o melhor desempenho para a sua carga de trabalho do armazém de dados. Geração 2 apresenta uma nova cache de disco de estado sólido NVMe que mantém os dados acedidos mais frequentemente próximos das CPUs. Esta ação remove a e/s remoto das cargas de trabalho mais intensiva e exigentes. Além do desempenho, a geração 2 oferece o maior nível de dimensionamento ao permitir Dimensionar até 30.000 unidades do Data Warehouse e ao fornecer o armazenamento em colunas ilimitado. Iremos ainda oferecer suporte à geração anterior (geração 1) do SQL Data Warehouse e manter os mesmos recursos; No entanto, é recomendável que [atualizar para ger2](upgrade-to-latest-generation.md) o quanto. 

## <a name="data-warehouse-capacity-settings"></a>Definições de capacidade do armazém de dados
As tabelas seguintes mostram a capacidade máxima para o armazém de dados em diferentes níveis de desempenho. Para alterar o nível de desempenho, consulte [dimensionar a computação - portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Ger2

Geração 2 fornece 2,5 vezes mais memória por consulta do que a geração 1. Esta memória extra ajuda a geração 2 fornecer seu rápido desempenho.  Os níveis de desempenho para o intervalo de geração 2 de DW100c a DW30000c. 

| Nível de desempenho | Nós de computação | Distribuições por nó de computação | Memória por armazém de dados (GB) |
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
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

A DWU de geração 2 máximo é DW30000c, que tem 60 nós de computação e de uma distribuição por nó de computação. Por exemplo, um armazém de dados de 600 TB em DW30000c processa aproximadamente 10 TB por nó de computação.

### <a name="gen1"></a>Ger1

Os níveis de serviço para o intervalo de geração 1 de DW100 a DW6000. 

| Nível de desempenho | Nós de computação | Distribuições por nó de computação | Memória por armazém de dados (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Valores máximos de simultaneidade
Para garantir que cada consulta tem recursos suficientes para executar com eficiência, o SQL Data Warehouse controla a utilização de recursos através da atribuição de blocos de simultaneidade para cada consulta. O sistema coloca consultas numa fila com base na importância e blocos de simultaneidade. Consultas de aguardar na fila até que existem suficientes ranhuras de simultaneidade disponíveis. [Importância](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) e blocos de simultaneidade e determinam a priorização de CPU. Para obter mais informações, consulte [analisar sua carga de trabalho](analyze-your-workload.md)

### <a name="gen2"></a>Ger2
 
**Classes estáticas**

A tabela seguinte mostra as consultas em simultâneo máximas e blocos de simultaneidade para cada [classe de recursos estáticos](resource-classes-for-workload-management.md).  

| Nível de Serviço | Consultas em simultâneo máximas | Ranhuras de simultaneidade disponíveis | Ranhuras utilizadas pelo staticrc10 | Ranhuras utilizadas pelo staticrc20 | Ranhuras utilizadas pelo staticrc30 | Ranhuras utilizadas pelo staticrc40 | Ranhuras utilizadas pelo staticrc50 | Ranhuras utilizadas pelo staticrc60 | Ranhuras utilizadas pelo staticrc70 | Ranhuras utilizadas pelo staticrc80 |
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

**Classes de dinâmica de recursos**

> [!NOTE]
> A classe de recursos de smallrc no ger2 adiciona dinamicamente memória à medida que o nível de serviço aumenta e só suporta um máximo e 32 consultas em simultâneo em DW1000c e 4 e DW100c.  Assim que a instância é dimensionada para além dos DW1500c, os blocos de simultaneidade e a memória usada pelo smallrc aumenta à medida que os aumentos de nível de serviço. 
>
>

A tabela seguinte mostra as consultas em simultâneo máximas e blocos de simultaneidade para cada [classe de recursos dinâmica](resource-classes-for-workload-management.md). Ao contrário de geração 1, as classes de recursos dinâmicos no ger2 são verdadeiramente dinâmicos.  Geração 2 usa uma alocação de percentagem de memória de 3-10-22-70 para classes de recursos pequena-medium-grande-xlarge em todos os níveis de serviço.

| Nível de Serviço | Consultas em simultâneo máximas | Ranhuras de simultaneidade disponíveis | Ranhuras utilizadas pelo smallrc | Ranhuras utilizadas pelo mediumrc | Ranhuras utilizadas pelo largerc | Ranhuras utilizadas pelo xlargerc |
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



#### <a name="gen1"></a>Ger1

Classes estáticas

A tabela seguinte mostra as consultas em simultâneo máximas e blocos de simultaneidade para cada [classe de recursos estáticos](resource-classes-for-workload-management.md) nos **Gen1**.

| Nível de serviço  | Consultas em simultâneo máximas | Ranhuras de simultaneidade máximo | Ranhuras utilizadas pelo staticrc10 | Ranhuras utilizadas pelo staticrc20 | Ranhuras utilizadas pelo staticrc30 | Ranhuras utilizadas pelo staticrc40 | Ranhuras utilizadas pelo staticrc50 | Ranhuras utilizadas pelo staticrc60 | Ranhuras utilizadas pelo staticrc70 | Ranhuras utilizadas pelo staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Classes de dinâmica de recursos
> [!NOTE]
> A classe de recursos de smallrc na Gen1 aloca uma quantidade fixa de memória por consulta, de forma semelhante a staticrc10 de classe de recursos estáticos.  Uma vez smallrc é estático, ele tem a capacidade de dimensionar para 128 consultas em simultâneo. 
>
>

A tabela seguinte mostra as consultas em simultâneo máximas e blocos de simultaneidade para cada [classe de recursos dinâmica](resource-classes-for-workload-management.md) nos **Gen1**.

| Nível de serviço  | Consultas em simultâneo máximas | Ranhuras de simultaneidade disponíveis | Ranhuras utilizadas pelo smallrc | Ranhuras utilizadas pelo mediumrc | Ranhuras utilizadas pelo largerc | Ranhuras utilizadas pelo xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Quando for cumprida uma nesses limiares, novas consultas são colocados em fila e executadas numa base first in, First Out.  Como um consultas é concluído e o número de consultas e ranhuras fique abaixo dos limites, o SQL Data Warehouse versões consultas em fila. 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como tirar partido das classes de recursos para otimizar ainda mais a carga de trabalho, reveja os artigos seguintes:
* [Classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md)
* [Analisar a sua carga de trabalho](analyze-your-workload.md)

