---
title: Como selecionar o tamanho VM certo para o seu cluster Azure HDInsight
description: Saiba como selecionar o tamanho VM certo para o seu cluster HDInsight.
keywords: tamanhos vm, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73682207"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Selecionando o tamanho VM certo para o seu cluster Azure HDInsight

Este artigo discute como selecionar o tamanho VM certo para os vários nós no seu cluster HDInsight. 

Comece por entender como as propriedades de uma máquina virtual como o processamento de CPU, o tamanho da RAM e a latência da rede afetarão o processamento das suas cargas de trabalho. Em seguida, pense na sua aplicação e como combina com o que diferentes famílias VM são otimizadas. Certifique-se de que a família VM que gostaria de utilizar é compatível com o tipo de cluster que pretende implantar. Para obter uma lista de todos os tamanhos VM suportados e recomendados para cada tipo de cluster, consulte [as configurações de nó suportados por Azure HDInsight](hdinsight-supported-node-configuration.md). Por último, pode utilizar um processo de benchmarking para testar algumas cargas de trabalho da amostra e verificar qual o SKU dentro dessa família que é adequado para si.

Para obter mais informações sobre o planeamento de outros aspetos do seu cluster, tais como a seleção de um tipo de armazenamento ou tamanho de cluster, consulte [o planeamento da capacidade para clusters HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Propriedades VM e cargas de trabalho de big data

O tamanho e tipo VM é determinado pelo poder de processamento do CPU, tamanho RAM e latência da rede:

- CPU: O tamanho VM dita o número de núcleos. Quanto mais núcleos, maior o grau de computação paralela que cada nó pode alcançar. Além disso, alguns tipos de VM têm núcleos mais rápidos.

- RAM: O tamanho VM também dita a quantidade de RAM disponível no VM. Para cargas de trabalho que armazenam dados na memória para processamento, em vez de ler em disco, certifique-se de que os seus nós de trabalhador têm memória suficiente para encaixar os dados.

- Rede: Para a maioria dos tipos de clusters, os dados processados pelo cluster não estão no disco local, mas sim num serviço de armazenamento externo, como o Data Lake Storage ou o Azure Storage. Considere a largura de banda da rede e a produção entre o nó VM e o serviço de armazenamento. A largura de banda de rede disponível para um VM normalmente aumenta com tamanhos maiores. Para mais detalhes, consulte [a visão geral dos tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Compreensão da otimização do VM

As famílias de máquinas virtuais em Azure são otimizadas para se adequarem a diferentes casos de uso. Na tabela abaixo, você pode encontrar alguns dos casos de uso mais populares e as famílias VM que combinam com eles.

| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Nível de entrada](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Tenha configurações de desempenho e memória de CPU mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. São económicos e oferecem uma opção de baixo custo para começar com o Azure. |
| [Fins gerais](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Proporção equilibrada entre CPU e memória. Ideais para testes e desenvolvimento, bases de dados pequenas a médias e servidores Web de tráfego baixo a médio. |
| [Com otimização de computação](../virtual-machines/linux/sizes-compute.md)        | F           | Proporção elevada de CPU para memória. Bom para servidores web de tráfego médio, aparelhos de rede, processos de lote e servidores de aplicações.        |
| [Com otimização de memória](../virtual-machines/linux/sizes-memory.md)         | Esv3  | Proporção elevada de memória para CPU. Ideais para servidores de bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |

- Para obter informações sobre os preços dos casos de VM disponíveis em todas as regiões apoiadas por HDInsight, consulte [o HdInsight Pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Tipos de VM de poupança de custos para cargas de trabalho leves

Se tiver requisitos de processamento leves, a [série F](https://azure.microsoft.com/blog/f-series-vm-size/) pode ser uma boa escolha para começar com o HDInsight. A um preço de tabela por hora mais baixo, a série F é o melhor valor de desempenho por preço no portefólio do Azure com base na Unidade de Computação do Azure (ACU) por vCPU.

A tabela seguinte descreve os tipos de cluster e os tipos de nós, que podem ser criados com os VMs da série Fsv2.

| Tipo de Cluster | Versão | Nó operário | Nó de cabeça | Nó do zookeeper |
|---|---|---|---|---|
| Spark | Todos | F4 e acima | não | não |
| Hadoop | Todos | F4 e acima | não | não |
| Kafka | Todos | F4 e acima | não | não |
| HBase | Todos | F4 e acima | não | não |
| LLAP | incapacitados | não | não | não |
| Storm | incapacitados | não | não | não |
| Serviço ML | HDI 3.6 APENAS | F4 e acima | não | não |

Para ver as especificações de cada SKU da série F, consulte os [tamanhos VM da série F](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Benchmarking

O benchmarking é o processo de execução de cargas de trabalho simuladas em diferentes VMs para medir o seu desempenho para as suas cargas de trabalho de produção. 

Para obter mais informações sobre o benchmarking para SKUs VM e tamanhos de cluster, consulte [o planeamento da capacidade do Cluster em Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Passos seguintes

- [Configurações de nó suportado por Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Sizes for Linux virtual machines in Azure](../virtual-machines/linux/sizes.md)
