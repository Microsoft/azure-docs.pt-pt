---
title: Armazenamento analítico e transacional distribuído globalmente para contêineres de Cosmos do Azure
description: Saiba mais sobre armazenamento transacional e analítico e suas opções de configuração para contêineres de Cosmos do Azure.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756910"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Armazenamento analítico e transacional distribuído globalmente para contêineres de Cosmos do Azure

O contêiner Cosmos do Azure é apoiado internamente por dois mecanismos de armazenamento – mecanismo de armazenamento transacional e um mecanismo de armazenamento analítico atualizável. Ambos os mecanismos de armazenamento são estruturados em log e otimizados para gravação para atualizações mais rápidas. No entanto, cada um deles é codificado de forma diferente:

* **Mecanismo de armazenamento transacional** – ele é codificado em formato orientado a linha para leituras e consultas transacionais rápidas.

* **Mecanismo de armazenamento analítico** -ele é codificado no formato colunar para consultas e verificações analíticas rápidas.

![Mecanismos de armazenamento e mapeamento de API de Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

O mecanismo de armazenamento transacional tem o respaldo do SSDs local, enquanto o armazenamento analítico é armazenado em um armazenamento de SSD fora do cluster de baixo custo. A tabela a seguir captura as diferenças notáveis entre o armazenamento transacional e o de análise.


|Funcionalidade  |Armazenamento transacional  |Armazenamento analítico |
|---------|---------|---------|
|Armazenamento máximo por um contêiner Cosmos do Azure |   Ilimitado      |    Ilimitado     |
|Armazenamento máximo por uma chave de partição lógica   |   10 GB      |   Ilimitado      |
|Codificação de armazenamento  |   Orientado por linha, usando um formato interno.   |   Orientado por coluna, usando o formato Apache parquet. |
|Localidade de armazenamento |   Armazenamento replicado apoiado por SSDs local/dentro do cluster. |  Armazenamento replicado apoiado por SSDs de baixo custo remoto/fora do cluster.       |
|Durabilidade  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|APIs que acessam os dados  |   SQL, MongoDB, Cassandra, Gremlin, tabelas e etcd.       | Apache Spark         |
|Retenção (tempo de vida ou TTL)   |  Orientado por política, configurado no contêiner Cosmos do Azure usando a propriedade `DefaultTimeToLive`.       |   Orientado por política, configurado no contêiner Cosmos do Azure usando a propriedade `ColumnStoreTimeToLive`.      |
|Preço por GB    |   US $0,25/GB      |  US $0,02/GB       |
|Preço para transações de armazenamento    | A taxa de transferência provisionada é cobrada a $0.08 por 100 RU/s com cobrança por hora.        |  A taxa de transferência baseada em consumo é cobrada a $0.05 para transações de gravação de 10.000 e $0.04 para transações de leitura de 10.000.       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Benefícios do armazenamento analítico e transacional

### <a name="no-etl-operations"></a>Nenhuma operação de ETL

Pipelines analíticos tradicionais são complexos com vários estágios, cada um exigindo operações de ETL (extração de transformação e carregamento) de e para as camadas de computação e armazenamento. Isso resulta em arquiteturas de processamento de dados complexas. Isso significa altos custos para vários estágios de armazenamento e computação e alta latência devido a grandes volumes de dados transferidos entre vários estágios de armazenamento e computação.  

Não há sobrecarga de executar operações de ETL com Azure Cosmos DB. Cada contêiner Cosmos do Azure é apoiado por mecanismos de armazenamento transacionais e analíticos, e a transferência de dados entre o mecanismo de armazenamento transacional e analítico é feita no mecanismo de banco de dados e sem nenhum salto de rede. A latência e o custo resultantes são significativamente menores em comparação com as soluções analíticas tradicionais. E você obtém um único sistema de armazenamento distribuído globalmente para cargas de trabalho transacionais e analíticas.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Armazenar várias versões, atualizar e consultar o armazenamento analítico

O armazenamento analítico é totalmente atualizável e contém o histórico de versão completo de todas as atualizações transacionais ocorridas no contêiner Cosmos do Azure.

Qualquer atualização feita ao armazenamento transacional é garantida para ser visível para o armazenamento analítico dentro de 30 segundos. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Armazenamento analítico de vários mestres, distribuído globalmente

Se sua conta do Azure Cosmos estiver no escopo de uma única região, os dados armazenados (em armazenamento transacional e analítico) nos contêineres também serão delimitados para uma única região. Por outro lado, se a conta do Azure Cosmos for distribuída globalmente, os dados armazenados nos contêineres também serão distribuídos globalmente.

Para contas do Azure Cosmos configuradas com várias regiões de gravação, as gravações no contêiner (para o armazenamento transacional e o de análise) são sempre executadas na região local e, portanto, são rápidas.

Para contas de Cosmos único ou de várias regiões do Azure, independentemente de se uma única região de gravação (mestre único) ou várias regiões de gravação (também conhecidas como vários mestres), as leituras/consultas analíticas e transacionais são executadas localmente na região especificada.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolamento de desempenho entre cargas de trabalho transacionais e analíticas

Em uma determinada região, as cargas de trabalho transacionais operam no armazenamento transacional/de linha do contêiner. Por outro lado, as cargas de trabalho analíticas operam em relação ao armazenamento analítico/de coluna do contêiner. Os dois mecanismos de armazenamento operam de forma independente e fornecem isolamento de desempenho estrito entre as cargas de trabalho.

As cargas de trabalho transacionais consomem a taxa de transferência provisionada (RUs). Ao contrário das cargas de trabalho transacionais, a taxa de transferência das cargas de trabalho analíticas é baseada no consumo real. As cargas de trabalho analíticas consomem recursos sob demanda.

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Instantâneos sob demanda e análise de viagens de tempo

Você pode tirar instantâneos de seus dados armazenados no armazenamento analítico de seus contêineres de Cosmos do Azure, a qualquer momento, chamando o comando `CreateSnapshot (name, timestamp)` no contêiner. Os instantâneos são nomeados como "indicadores" no histórico das atualizações que já foram feitas em seu contêiner.

![Instantâneos sob demanda e análise de viagens de tempo](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

No momento da criação do instantâneo, além do nome, você pode especificar o carimbo de data/hora que define o estado do seu contêiner no histórico de atualizações. Em seguida, você pode carregar os dados do instantâneo no Spark e executar as consultas.

No momento, você só pode tirar instantâneos sob demanda a qualquer momento no contêiner, a capacidade de fazer automaticamente instantâneos com base em uma agenda ou política personalizada ainda não tem suporte.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Configurar e hierarquizar dados entre armazenamentos transacionais e analíticos de forma independente

Dependendo do seu cenário, você pode habilitar ou desabilitar de forma independente cada um dos dois mecanismos de armazenamento. A seguir estão as configurações para cada cenário:

|Cenário |Configuração de armazenamento transacional  |Configuração de armazenamento analítico |
|---------|---------|---------|
|Executando exclusivamente cargas de trabalho analíticas (com retenção infinita) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Executando cargas de trabalho transacionais exclusivamente (com retenção infinita)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Executando cargas de trabalho transacionais e analíticas (com retenção infinita)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Execução de cargas de trabalho transacionais e analíticas (com intervalos de retenção diferentes, também conhecidos como camadas de armazenamento)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Configurar o contêiner exclusivamente para cargas de trabalho analíticas (com retenção infinita)**

   Você pode configurar o contêiner Cosmos do Azure exclusivamente para cargas de trabalho analíticas. Essa configuração tem uma vantagem em que você não precisa pagar pelo armazenamento transacional. Se seu objetivo é usar o contêiner somente para cargas de trabalho analíticas, você pode desabilitar o armazenamento transacional definindo `DefaultTimeToLive` como 0 no contêiner Cosmos e pode habilitar o armazenamento analítico com retenção infinita definindo `ColumnStoreTimeToLive` como-1.

   ![Cargas de trabalho analíticas com retenção infinita](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Configurar o contêiner exclusivamente para cargas de trabalho transacionais (com retenção infinita)**

   Você pode configurar seu contêiner Cosmos do Azure exclusivamente para cargas de trabalho transacionais. Você pode desabilitar o armazenamento analítico definindo `ColumnStoreTimeToLive` como 0 no contêiner e pode habilitar o armazenamento analítico com retenção infinita definindo `DefaultTimeToLive` como-1.

   ![Cargas de trabalho transacionais com retenção infinita](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Configurar o contêiner para cargas de trabalho transacionais e analíticas (com retenção infinita)**

   Você pode configurar o contêiner Cosmos do Azure para cargas de trabalho transacionais e analíticas com isolamento de desempenho total entre eles. Você pode habilitar o armazenamento analítico definindo `ColumnStoreTimeToLive` como-1 e habilitar o armazenamento transacional com retenção infinita definindo `DefaultTimeToLive ` como-1.

   ![Cargas de trabalho transacionais e analíticas com retenção infinita](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Configurar o contêiner para cargas de trabalho transacionais e analíticas com camadas de armazenamento**

   Você pode configurar o contêiner Cosmos do Azure para cargas de trabalho transacionais e analíticas com isolamento de desempenho total entre eles com intervalos de retenção diferentes. Azure Cosmos DB impedirá que seu armazenamento analítico seja sempre retido por uma duração maior do que o armazenamento transacional.

   Você pode habilitar o armazenamento transacional com retenção infinita definindo `DefaultTimeToLive` como < valor 1 > e habilitar o armazenamento analítico Configurando `ColumnStoreTimeToLive` para < valor 2 >. Azure Cosmos DB irá impor que < valor 2 > seja sempre maior que < valor 1 >.

   ![Cargas de trabalho transacionais e analíticas com camadas de armazenamento](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Passos seguintes

* [Vida útil em Azure Cosmos DB](time-to-live.md)
