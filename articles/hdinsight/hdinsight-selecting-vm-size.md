---
title: Como selecionar o tamanho certo da VM para o cluster HDInsight do Azure
description: Saiba como selecionar o tamanho correto da VM para seu cluster HDInsight.
keywords: tamanhos de VM, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682207"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Selecionando o tamanho correto da VM para o cluster HDInsight do Azure

Este artigo discute como selecionar o tamanho correto da VM para os vários nós em seu cluster HDInsight. 

Comece compreendendo como as propriedades de uma máquina virtual, como o processamento da CPU, o tamanho da RAM e a latência da rede, afetarão o processamento de suas cargas de trabalho. Em seguida, pense em seu aplicativo e como ele corresponde ao que as diferentes famílias de VM são otimizadas para o. Certifique-se de que a família de VMs que você deseja usar seja compatível com o tipo de cluster que você planeja implantar. Para obter uma lista de todos os tamanhos de VM compatíveis e recomendados para cada tipo de cluster, consulte [configurações de nó com suporte do Azure HDInsight](hdinsight-supported-node-configuration.md). Por fim, você pode usar um processo de benchmark para testar algumas cargas de trabalho de exemplo e verificar qual SKU dentro dessa família é ideal para você.

Para obter mais informações sobre como planejar outros aspectos do cluster, como selecionar um tipo de armazenamento ou tamanho de cluster, consulte [planejamento de capacidade para clusters HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Propriedades da VM e cargas de trabalho de Big Data

O tamanho e o tipo da VM são determinados pelo poder de processamento da CPU, pelo tamanho da RAM e pela latência de rede:

- CPU: o tamanho da VM determina o número de núcleos. Quanto mais núcleos, maior o grau de computação paralela que cada nó pode atingir. Além disso, alguns tipos de VM têm núcleos mais rápidos.

- RAM: o tamanho da VM também determina a quantidade de RAM disponível na VM. Para cargas de trabalho que armazenam dados na memória para processamento, em vez de ler do disco, verifique se os nós de trabalho têm memória suficiente para se ajustarem aos dados.

- Rede: para a maioria dos tipos de cluster, os dados processados pelo cluster não estão no disco local, mas sim em um serviço de armazenamento externo, como o Data Lake Storage ou o armazenamento do Azure. Considere a largura de banda da rede e a taxa de transferência entre a VM do nó e o serviço de armazenamento. A largura de banda de rede disponível para uma VM normalmente aumenta com tamanhos maiores. Para obter detalhes, consulte [visão geral de tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Noções básicas sobre a otimização da VM

As famílias de máquinas virtuais no Azure são otimizadas para atender a diferentes casos de uso. Na tabela a seguir, você pode encontrar alguns dos casos de uso mais populares e as famílias de VMs que correspondem a eles.

| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Nível de entrada](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Ter configurações de desempenho e memória da CPU mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste. São económicas e fornecem uma opção de baixo custo para começar a utilizar o Azure. |
| [Fins gerais](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Relação CPU/memória equilibrada. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. |
| [Com otimização de computação](../virtual-machines/linux/sizes-compute.md)        | F           | Relação CPU/memória elevada. Ideal para servidores Web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações.        |
| [Com otimização de memória](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Alta taxa de memória/CPU. Ideal para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.                 |

- Para obter informações sobre os preços das instâncias de VM disponíveis em regiões com suporte do HDInsight, consulte [preços do hdinsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Tipos de VM de economia para cargas de trabalho leves

Se você tiver requisitos leves de processamento, a [série F](https://azure.microsoft.com/blog/f-series-vm-size/) pode ser uma boa opção para começar a usar o HDInsight. A um preço de tabela por hora mais baixo, a série F é o melhor valor de desempenho por preço no portefólio do Azure com base na Unidade de Computação do Azure (ACU) por vCPU.

A tabela a seguir descreve os tipos de cluster e tipos de nó, que podem ser criados com as VMs da série Fsv2.

| Tipo de Cluster | Versão | Nó de Trabalho | Nó Principal | Nó de ZooKeeper |
|---|---|---|---|---|
| Spark | Todos | F4 e acima | não | não |
| Hadoop | Todos | F4 e acima | não | não |
| Kafka | Todos | F4 e acima | não | não |
| HBase | Todos | F4 e acima | não | não |
| LLAP | desabilitado | não | não | não |
| Storm | desabilitado | não | não | não |
| Serviço ML | SOMENTE HDI 3,6 | F4 e acima | não | não |

Para ver as especificações de cada SKU da série F, consulte [tamanhos de VM da série f](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Comparação

O benchmark é o processo de execução de cargas de trabalho simuladas em diferentes VMs para medir o quão bem eles serão executados para suas cargas de trabalho de produção. 

Para obter mais informações sobre o benchmarking para SKUs de VM e tamanhos de cluster, consulte [planejamento de capacidade de cluster no Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Passos seguintes

- [Configurações de nó com suporte do Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Tamanhos de máquinas virtuais do Windows no Azure](../virtual-machines/linux/sizes.md)
