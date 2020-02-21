---
title: Configurações de nó suportadas pelo Azure HDInsight
description: Aprenda as configurações mínimas e recomendadas para os nós do cluster HDInsight.
keywords: tamanhos vm, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484791"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações padrão e recomendadas para o Azure HDInsight?

Este artigo discute configurações de nós padrão e recomendadas para clusters Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração padrão e mínima recomendada do nó e tamanhos de máquina virtual para clusters

As tabelas a seguir listam tamanhos padrão e recomendados de máquina virtual (VM) para clusters HDInsight.  Esta informação é necessária para entender os tamanhos vm a utilizar quando estiver a criar scripts PowerShell ou Azure CLI para implantar clusters HDInsight.

Se precisar de mais de 32 nós de trabalhador num cluster, selecione um tamanho de nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM. 

Os únicos tipos de cluster que têm discos de dados são os clusters Kafka e HBase com a funcionalidade Accelerated Writes ativada. O HDInsight suporta os tamanhos do disco P30 e S30 nestes cenários.

As especificações de todos os tipos mínimos de VM recomendados utilizados neste documento são resumidas na tabela seguinte.

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | Max NICs / Largura de banda de rede esperada (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

Para obter mais detalhes sobre as especificações de cada tipo VM, consulte os seguintes documentos:

* [Tamanhos de máquina virtual de propósito geral: Série Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Tamanhos otimizados da memória da máquina: Série Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Tamanhos de máquina virtual de propósito geral: Série Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões apoiadas, exceto Brasil sul e Japão oeste

> [!Note]
> Para obter o identificador SKU para uso em powershell e outros scripts, adicione `Standard_` ao início de todas as VM SKUs nas tabelas abaixo. Por exemplo, `D12_v2` tornar-se-ia `Standard_D12_v2`.

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | Servidor ML | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeça: tamanho VM padrão | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Cabeça: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Trabalhador: tamanho vm padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por corretor |
| Trabalhador: tamanhomínimo recomendado vM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: tamanho vm padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos mínimos recomendados de VM |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços ML: tamanho VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços ML: tamanho mínimo recomendado vM |  |  |  |  |  | D4_v2 |  |

\* = Tamanhos VM para clusters do Pacote de Segurança Empresarial spark (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brasil sul e Japão apenas a oeste

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | Serviços ML |
|---|---|---|---|---|---|---|
| Cabeça: tamanho VM padrão | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeça: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalhador: tamanho vm padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalhador: tamanhomínimo recomendado vM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: tamanho vm padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos mínimos recomendados de VM |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços ML: tamanhos VM padrão |  |  |  |  |  | D4 |
| Serviços ML: tamanhos mínimos de VM recomendados |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - A cabeça é conhecida como *Nimbus* para o tipo de aglomerado de tempestade.
> - O trabalhador é conhecido como *Supervisor* do tipo aglomerado de tempestades.
> - O trabalhador é conhecido como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Passos Seguintes

* [Quais são os componentes e versões Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)
