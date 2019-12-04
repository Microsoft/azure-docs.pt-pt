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
ms.openlocfilehash: 278639b27d821e8d6440248a1add43bcd9de22c6
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775230"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações de nó padrão e recomendadas para o Azure HDInsight?

Este artigo discute as configurações de nó padrão e recomendadas para clusters do Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração de nó padrão e mínimo recomendado e tamanhos de máquina virtual para clusters

As tabelas a seguir listam os tamanhos de VM (máquina virtual) padrão e recomendados para clusters HDInsight.  Essas informações são necessárias para entender os tamanhos de VM a serem usados ao criar o PowerShell ou CLI do Azure scripts para implantar clusters HDInsight.

Se você precisar de mais de 32 nós de trabalho em um cluster, selecione um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM. 

Os únicos tipos de cluster que têm discos de dados são Kafka e clusters HBase com o recurso de gravações aceleradas habilitado. O HDInsight dá suporte a tamanhos de disco p30 e S30 nesses cenários.

As especificações de todos os tipos de VM mínimos recomendados usados neste documento são resumidas na tabela a seguir.

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

Para obter mais detalhes sobre as especificações de cada tipo de VM, consulte os seguintes documentos:

* [Tamanhos de máquina virtual de uso geral: série Dv2 1-5](../virtual-machines/linux/sizes-general.md#dv2-series)
* [Tamanhos de máquina virtual com otimização de memória: série Dv2 11-15](../virtual-machines/linux/sizes-memory.md#dv2-series-11-15)
* [Tamanhos de máquina virtual de uso geral: série Av2 1-8](../virtual-machines/linux/sizes-general.md#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões com suporte, exceto sul do Brasil e oeste do Japão

> [!Note]
> Para obter o identificador de SKU para uso no PowerShell e outros scripts, adicione `Standard_` ao início de todas as SKUs de VM nas tabelas abaixo. Por exemplo, `D12_v2` se tornaria `Standard_D12_v2`.

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Cabeçalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Trabalho: tamanho de VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por agente |
| Trabalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos mínimos de VM recomendados |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços de ML: tamanho de VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços de ML: tamanho mínimo recomendado da VM |  |  |  |  |  | D4_v2 |  |

\* = tamanhos de VM para clusters do Spark Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Sul do Brasil e oeste do Japão somente

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | Serviços ML |
|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeçalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalho: tamanho de VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalho: tamanhos mínimos de VM recomendados | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos mínimos de VM recomendados |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços de ML: tamanhos de VM padrão |  |  |  |  |  | D4 |
| Serviços de ML: tamanhos mínimos de VM recomendados |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - O Worker é conhecido como *supervisor* para o tipo de cluster Storm.
> - O Worker é conhecido como *região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Passos seguintes

* [Quais são os componentes Apache Hadoop e as versões disponíveis com o HDInsight?](hdinsight-component-versioning.md)
