---
title: Armazenamento transacional e analítico distribuído globalmente (em pré-visualização privada) para contentores Azure Cosmos
description: Saiba mais sobre o armazenamento transacional e analítico e as suas opções de configuração para os contentores Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623387"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Armazenamento transacional e analítico distribuído globalmente para contentores Azure Cosmos

O recipiente Azure Cosmos é apoiado internamente por dois motores de armazenamento - motor de armazenamento transacional e um motor de armazenamento analítico updatable (em pré-visualização privada). Ambos os motores de armazenamento são estruturados por log e otimizados para atualizações mais rápidas. No entanto, cada um deles é codificado de forma diferente:

* Motor de **armazenamento transacional** – Está codificado em formato orientado para linhas para leituras e consultas rápidas de transação.

* Motor de **armazenamento analítico** - Está codificado em formato colunaar para consultas e digitalizações analíticas rápidas.

![Motores de armazenamento e mapeamento da API Da API da Azure Cosmos](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

O motor de armazenamento transacional é apoiado por SSDs locais, enquanto o armazenamento analítico é armazenado num armazenamento ssd barato fora do cluster. A tabela seguinte capta as diferenças notáveis entre o armazenamento transacional e o armazenamento analítico.


|Funcionalidade  |Armazenamento transacional  |Armazenamento analítico |
|---------|---------|---------|
|Armazenamento máximo por um recipiente Azure Cosmos |   Ilimitado      |    Ilimitado     |
|Armazenamento máximo por chave de partição lógica   |   20 GB      |   Ilimitado      |
|Codificação de armazenamento  |   Orientado para a linha, utilizando um formato interno.   |   Orientado para colunas, utilizando o formato Apache Parquet. |
|Localidade de armazenamento |   Armazenamento replicado apoiado por SSDs locais/intra-cluster. |  Armazenamento replicado apoiado por SSDs remotos/off-cluster baratos.       |
|Durabilidade  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|APIs que acedem aos dados  |   SQL, MongoDB, Cassandra, Gremlin, Mesas e Etc.       | Apache Spark         |
|Retenção (Tempo de viver ou TTL)   |  Orientado pela política, configurado no contentor Azure Cosmos utilizando a `DefaultTimeToLive` propriedade.       |   Orientado pela política, configurado no contentor Azure Cosmos utilizando a `ColumnStoreTimeToLive` propriedade.      |
|Preço por GB    |   Ver a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Ver a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Preço das transações de armazenamento    |  Ver a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Ver a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Benefícios do armazenamento transacional e analítico

### <a name="no-etl-operations"></a>Sem operações da ETL

Os oleodutos analíticos tradicionais são complexos com múltiplas fases cada uma que requer operações de Extracção-Transformação-Carga (ETL) de e para os níveis de computação e armazenamento. Resulta em complexas arquiteturas de processamento de dados. O que significa custos elevados para várias fases de armazenamento e computação, e alta latência devido a volumes maciços de dados transferidos entre várias fases de armazenamento e cálculo.  

Não há nenhuma sobrecarga de executar operações da ETL com a Azure Cosmos DB. Cada recipiente Azure Cosmos é apoiado por motores de armazenamento transacionais e analíticos, e a transferência de dados entre o motor de armazenamento transacional e analítico é feita dentro do motor de base de dados, e sem qualquer lúpulo de rede. A latência e o custo resultantes são significativamente mais baixos em comparação com as soluções analíticas tradicionais. E obtém-se um único sistema de armazenamento distribuído globalmente para cargas de trabalho transacionais e analíticas.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Armazenar várias versões, atualizar e consultar o armazenamento analítico

O armazenamento analítico é totalmente atualizado, e contém o histórico completo da versão de todas as atualizações transacionais que ocorreram no contentor Azure Cosmos.

Qualquer atualização feita ao armazenamento transacional é garantidamente visível para o armazenamento analítico dentro de 30 segundos. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Armazenamento analítico multi-master distribuído globalmente

Se a sua conta Azure Cosmos for remelítica para uma única região, os dados armazenados (em armazenamento transacional e analítico) nos contentores também são remetos para uma única região. Por outro lado, se a conta Azure Cosmos for distribuída globalmente, os dados armazenados nos contentores também são distribuídos globalmente.

Para as contas da Azure Cosmos configuradas com várias regiões de escrita, os escritos para o contentor (tanto para o armazenamento transacional como para o armazenamento analítico) são sempre realizados na região local e, portanto, são rápidos.

Para as contas azure cosmos únicas ou multi-regiões, independentemente de regiões únicas de escrita (master único) ou múltiplas regiões de escrita (também conhecidas como multi-mestre), tanto as leituras/consultas transacionais como analíticas são realizadas localmente na determinada região.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolamento de desempenho entre cargas de trabalho transacionais e analíticas

Numa determinada região, as cargas de trabalho transacionais operam contra o armazenamento transacional/fila do seu contentor. Por outro lado, as cargas de trabalho analíticas funcionam contra o armazenamento analítico/coluna do seu recipiente. Os dois motores de armazenamento funcionam de forma independente e proporcionam um rigoroso isolamento de desempenho entre as cargas de trabalho.

As cargas de trabalho transacionais consomem o consumo de receitas (UI). Ao contrário das cargas de trabalho transacionais, a produção de cargas de trabalho analíticas baseia-se no consumo real. As cargas de trabalho analíticas consomem recursos a pedido.

## <a name="next-steps"></a>Passos seguintes

* [Hora de viver em Azure Cosmos DB](time-to-live.md)
