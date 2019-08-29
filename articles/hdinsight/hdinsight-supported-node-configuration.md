---
title: Configurações de nó com suporte do Azure HDInsight
description: Conheça as configurações mínimas e recomendadas para nós de cluster HDInsight.
keywords: tamanhos de VM, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076215"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações de nó padrão e recomendadas para o Azure HDInsight?

Este artigo discute as configurações de nó padrão e recomendadas para clusters do Azure HDInsight.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração de nó padrão e recomendada e tamanhos de máquina virtual para clusters

As tabelas a seguir listam os tamanhos de VM (máquina virtual) padrão e recomendados para clusters HDInsight.  Essas informações são necessárias para entender os tamanhos de VM a serem usados ao criar o PowerShell ou CLI do Azure scripts para implantar clusters HDInsight. 

Se você precisar de mais de 32 nós de trabalho em um cluster, selecione um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM. 

Os únicos tipos de cluster que têm discos de dados são Kafka e clusters HBase com o recurso de gravações aceleradas habilitado. O HDInsight dá suporte a tamanhos de disco p30 e S30 nesses cenários.

Para obter mais detalhes sobre a especificação de cada tipo de VM, consulte os seguintes documentos:

* [Tamanhos de máquina virtual de uso geral: Série Dv2 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Tamanhos de máquina virtual com otimização de memória: Série Dv2 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Tamanhos de máquina virtual de uso geral: Série Av2 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões com suporte, exceto sul do Brasil e oeste do Japão

> [!Note]
> Para obter o identificador de SKU para uso no PowerShell e outros scripts, `Standard_` adicione ao início de todos os SKUs de VM nas tabelas a seguir. Por exemplo, `D12_v2` se tornaria. `Standard_D12_v2`

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Cabeçalho: tamanhos de VM recomendados | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Trabalho: tamanho de VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por agente |
| Trabalho: tamanhos de VM recomendados | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos de VM recomendados |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| Serviços de ML: tamanho de VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços de ML: tamanho de VM recomendado |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Sul do Brasil e oeste do Japão somente

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeçalho: tamanhos de VM recomendados | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Trabalho: tamanho de VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalho: tamanhos de VM recomendados | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos de VM recomendados |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| Serviços de ML: tamanhos de VM padrão |  |  |  |  |  | D4 |
| Serviços de ML: tamanhos de VM recomendados |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - O Worker é conhecido como *supervisor* para o tipo de cluster Storm.
> - O Worker é conhecido como *região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Passos seguintes

* [Quais são os componentes do Apache Hadoop e versões disponíveis com o HDInsight?](hdinsight-component-versioning.md)
