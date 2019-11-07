---
title: Armazenamento analítico e transacional distribuído globalmente para contêineres de Cosmos do Azure
description: Saiba mais sobre armazenamento transacional e analítico e suas opções de configuração para contêineres de Cosmos do Azure.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 22bb36e3b22f65bbf9922bd31e4b2e041cdb8979
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601224"
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
|Preço por GB    |   Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Preço para transações de armazenamento    |  Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

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

## <a name="next-steps"></a>Passos seguintes

* [Vida útil em Azure Cosmos DB](time-to-live.md)
