---
title: Configurações de nó suportado por Azure HDInsight
description: Aprenda as configurações mínimas e recomendadas para os nós de cluster HDInsight.
keywords: tamanhos vm, tamanhos de cluster, configuração de cluster
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931641"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações de nó padrão e recomendadas para Azure HDInsight?

Este artigo discute configurações de nó padrão e recomendadas para clusters Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração de nó recomendado predefinido e mínimo recomendado e tamanhos de máquina virtual para clusters

As tabelas que se seguem listam os tamanhos padrão e recomendados da máquina virtual (VM) para clusters HDInsight.  Esta informação é necessária para entender os tamanhos VM para usar quando estiver a criar scripts PowerShell ou Azure CLI para implantar clusters HDInsight.

Se precisar de mais de 32 nós de trabalhador num cluster, selecione um nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM.

Os únicos tipos de cluster que têm discos de dados são os clusters Kafka e HBase com a funcionalidade De Escritas Aceleradas ativadas. HDInsight suporta tamanhos de disco P30 e S30 nestes cenários. Para todos os outros tipos de cluster, o HDInsight fornece espaço de disco gerido com o cluster. A partir de 11/07/2019, o tamanho do disco gerido de cada nó no cluster recém-criado é de 128 GB. Isto não pode ser mudado.

As especificações de todos os tipos de VM recomendados mínimos utilizados neste documento são resumidas no quadro seguinte.

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | Max NICs / Largura de banda esperada (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

Para obter mais informações sobre as especificações de cada tipo VM, consulte os seguintes documentos:

* [Tamanhos de máquina virtual de finalidade geral: Série Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Tamanhos de máquina virtual otimizados de memória: Série Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Tamanhos de máquina virtual para fins gerais: Série Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões apoiadas, exceto Brasil sul e Japão oeste

> [!Note]
> Para obter o identificador SKU para uso em powershell e outros scripts, adicione `Standard_` ao início de todos os SKUs VM nas tabelas abaixo. Por exemplo, `D12_v2` tornar-se-ia. `Standard_D12_v2`

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | Servidor ML | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeça: tamanho VM padrão | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Cabeça: tamanhos VM recomendados mínimos | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Trabalhador: tamanho VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por corretor |
| Trabalhador: tamanhos VM recomendados mínimos | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: tamanho VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos VM recomendados mínimos |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços ML: tamanho VM padrão |  |  |  |  |  | D4_v2 |  |
| Serviços ML: tamanho VM recomendado mínimo |  |  |  |  |  | D4_v2 |  |

\* = Tamanhos VM para clusters de pacotes de segurança empresarial spark (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brasil sul e Japão apenas a oeste

| Tipo de cluster | Hadoop | HBase | Interactive Query | Storm | Spark | Serviços ML |
|---|---|---|---|---|---|---|
| Cabeça: tamanho VM padrão | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeça: tamanhos VM recomendados mínimos | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalhador: tamanho VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalhador: tamanhos VM recomendados mínimos | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: tamanho VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos VM recomendados mínimos |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços ML: tamanhos VM predefinidos |  |  |  |  |  | D4 |
| Serviços ML: tamanhos VM recomendados mínimos |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - A cabeça é conhecida como *Nimbus* para o tipo de aglomerado de tempestade.
> - O trabalhador é conhecido como *Supervisor* do tipo de aglomerado de tempestades.
> - O trabalhador é conhecido como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Passos seguintes

* [Quais são os componentes e versões Apache Hadoop disponíveis com HDInsight?](hdinsight-component-versioning.md)
