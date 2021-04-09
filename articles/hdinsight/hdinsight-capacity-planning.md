---
title: Planeamento da capacidade do cluster em Azure HDInsight
description: Identifique questões-chave para o planeamento de capacidade e desempenho de um cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933238"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planeamento da capacidade para clusters do HDInsight

Antes de implementar um cluster HDInsight, planeie a capacidade de cluster pretendida, determinando o desempenho e escala necessários. Este planeamento ajuda a otimizar tanto a usabilidade como os custos. Algumas decisões de capacidade de cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho mudarem, um cluster pode ser desmontado e recriado sem perder dados armazenados.

As principais questões a fazer para o planeamento da capacidade são:

* Em que região geográfica deve implantar o seu aglomerado?
* De quanto armazenamento precisa?
* Que tipo de cluster deve implantar?
* Que tamanho e tipo de máquina virtual (VM) devem utilizar os seus nós de cluster?
* Quantos nós de trabalhador deve ter o seu aglomerado?

## <a name="choose-an-azure-region"></a>Escolha uma região de Azure

A região de Azure determina onde o seu aglomerado é fisicamente a provisionado. Para minimizar a latência das leituras e das gravações, o cluster deve estar próximo dos seus dados.

HDInsight está disponível em muitas regiões do Azure. Para encontrar a região mais próxima, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)

## <a name="choose-storage-location-and-size"></a>Escolha o local de armazenamento e o tamanho

### <a name="location-of-default-storage"></a>Localização do armazenamento predefinido

O armazenamento predefinido, seja uma conta de Armazenamento Azure ou um armazenamento do Lago de Dados Azure, deve estar no mesmo local que o seu cluster. O Azure Storage está disponível em todos os locais. Data Lake Storage Gen1 está disponível em algumas regiões - consulte a disponibilidade atual de [armazenamento do Lago de Dados.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

### <a name="location-of-existing-data"></a>Localização dos dados existentes

Se pretender utilizar uma conta de armazenamento existente ou o Armazenamento do Lago de Dados como armazenamento padrão do seu cluster, então deve implantar o seu cluster nesse mesmo local.

### <a name="storage-size"></a>Tamanho do armazenamento

Num cluster implantado, pode anexar contas adicionais de Armazenamento Azure ou aceder a outros Data Lake Storage. Todas as suas contas de armazenamento devem viver no mesmo local que o seu cluster. Um armazenamento do Lago de Dados pode estar em um local diferente, embora grandes distâncias possam introduzir alguma latência.

O Azure Storage tem [alguns limites de capacidade,](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)enquanto a Data Lake Storage Gen1 é quase ilimitada.

Um cluster pode aceder a uma combinação de diferentes contas de armazenamento. Exemplos típicos incluem:

* Quando a quantidade de dados for suscetível de exceder a capacidade de armazenamento de um único recipiente de armazenamento de bolhas.
* Quando a taxa de acesso ao recipiente blob pode exceder o limiar onde ocorre estrangulamento.
* Quando quiser fazer dados, já fez o upload para um recipiente blob disponível para o cluster.
* Quando pretende isolar diferentes partes do armazenamento por razões de segurança ou simplificar a administração.

Para um melhor desempenho, utilize apenas um recipiente por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolha um tipo de cluster

O tipo de cluster determina a carga de trabalho que o seu cluster HDInsight está configurado para funcionar. Os tipos incluem [Apache Hadoop,](./hadoop/apache-hadoop-introduction.md) [Apache Storm,](./storm/apache-storm-overview.md) [Apache Kafka,](./kafka/apache-kafka-introduction.md)ou [Apache Spark.](./spark/apache-spark-overview.md) Para obter uma descrição detalhada dos tipos de cluster disponíveis, consulte [Introdução ao Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster tem uma topologia de implantação específica que inclui requisitos para o tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolha o tamanho e o tipo VM

Cada tipo de cluster tem um conjunto de tipos de nós, e cada tipo de nó tem opções específicas para o seu tamanho e tipo VM.

Para determinar o tamanho ideal do cluster para a sua aplicação, pode comparar a capacidade do cluster e aumentar o tamanho conforme indicado. Por exemplo, pode utilizar uma carga de trabalho simulada ou uma *consulta canária.* Execute as suas cargas de trabalho simuladas em diferentes agrupamentos de tamanhos. Aumente gradualmente o tamanho até que o desempenho pretendido seja atingido. Uma consulta canária pode ser inserida periodicamente entre as outras consultas de produção para mostrar se o cluster tem recursos suficientes.

Para obter mais informações sobre como escolher a família VM certa para a sua carga de trabalho, consulte [selecionar o tamanho VM certo para o seu cluster.](hdinsight-selecting-vm-size.md)

## <a name="choose-the-cluster-scale"></a>Escolha a escala de cluster

A escala de um cluster é determinada pela quantidade dos seus nós VM. Para todos os tipos de cluster, existem tipos de nó que têm uma escala específica, e tipos de nó que suportam a escala. Por exemplo, um cluster pode exigir exatamente três nós [Apache ZooKeeper](https://zookeeper.apache.org/) ou dois nós de cabeça. Os nós dos trabalhadores que fazem o processamento de dados de forma distribuída beneficiam dos nós de trabalho adicionais.

Dependendo do tipo de cluster, o aumento do número de nós dos trabalhadores adiciona capacidade computacional adicional (como mais núcleos). Mais nós aumentarão a memória total necessária para todo o cluster para suportar o armazenamento na memória dos dados que estão a ser tratados. Tal como acontece com a escolha do tamanho e do tipo VM, a seleção da escala de cluster certa é tipicamente alcançada empiricamente. Utilize cargas de trabalho simuladas ou consultas canárias.

Você pode escalar o seu cluster para atender às exigências de carga máxima. Em seguida, escaloná-lo de volta quando os nós extras já não são necessários. A [função Autoscale](hdinsight-autoscale-clusters.md) permite-lhe escalar automaticamente o seu cluster com base em métricas e timings pré-determinados. Para obter mais informações sobre a escala dos seus clusters manualmente, consulte [os clusters Scale HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

És cobrado para toda a vida de um aglomerado. Se houver apenas momentos específicos em que necessita do seu cluster, [crie clusters a pedido utilizando a Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Também pode criar scripts PowerShell que provisem e apaguem o seu cluster e, em seguida, agendar esses scripts usando [a Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando um cluster é eliminado, a sua metástore hive padrão também é eliminada. Para persistir a meta-loja para a próxima recriação do cluster, utilize uma loja de metadados externos, como a Base de Dados Azure ou [a Apache Oozie](https://oozie.apache.org/).

### <a name="isolate-cluster-job-errors"></a>Isolar erros de trabalho de cluster

Por vezes, podem ocorrer erros devido à execução paralela de múltiplos mapas e reduzir componentes num cluster multi-nó. Para ajudar a isolar o problema, experimente os testes distribuídos. Executar vários empregos simultâneos num único aglomerado de nóiros. Em seguida, expandir esta abordagem para executar múltiplos empregos simultaneamente em clusters contendo mais de um nó. Para criar um cluster HDInsight de nó único em Azure, utilize a *`Custom(size, settings, apps)`* opção e utilize um valor de 1 para número *de nós de trabalhadores* na secção tamanho do **Cluster** ao a provisionar um novo cluster no portal.

## <a name="quotas"></a>Quotas

Para obter mais informações sobre a gestão das quotas de subscrição, consulte [aumentos de quota de solicitação](quota-increase-request.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters em HDInsight com Apache Hadoop, Spark, Kafka, e muito mais](hdinsight-hadoop-provision-linux-clusters.md): Aprenda a configurar e configurar clusters em HDInsight.
* [Monitor cluster performance](hdinsight-key-scenarios-to-monitor.md): Saiba mais sobre cenários-chave para monitorizar o seu cluster HDInsight que pode afetar a capacidade do seu cluster.
