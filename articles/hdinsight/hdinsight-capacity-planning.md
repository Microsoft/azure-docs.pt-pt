---
title: Planeamento de capacidade de cluster em Azure HDInsight
description: Identifique questões-chave para o planeamento de capacidade e desempenho de um cluster Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932757"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planeamento de capacidade para clusters HDInsight

Antes de implantar um cluster HDInsight, planeie a capacidade de cluster desejada, determinando o desempenho e a escala necessários. Este planeamento ajuda a otimizar tanto a usabilidade como os custos. Algumas decisões de capacidade de cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho mudarem, um cluster pode ser desmontado e recriado sem perder dados armazenados.

As principais questões a colocar para o planeamento da capacidade são:

* Em que região geográfica deve implantar o seu aglomerado?
* De quanto armazenamento precisa?
* Que tipo de cluster deve implantar?
* Que tamanho e tipo de máquina virtual (VM) devem usar os seus nós de cluster?
* Quantos nós operários devem ter o seu agrupamento?

## <a name="choose-an-azure-region"></a>Escolha uma região de Azure

A região de Azure determina onde o seu cluster é fisicamente provisionado. Para minimizar a latência das leituras e dos escritos, o cluster deve estar próximo dos seus dados.

O HDInsight está disponível em muitas regiões do Azure. Para encontrar a região mais próxima, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)

## <a name="choose-storage-location-and-size"></a>Escolha a localização e o tamanho do armazenamento

### <a name="location-of-default-storage"></a>Localização do armazenamento por defeito

O armazenamento predefinido, quer uma conta de Armazenamento Azure quer o Armazenamento de Lagos De Dados Azure, deve estar no mesmo local que o seu cluster. O Azure Storage está disponível em todos os locais. Data Lake Storage Gen1 está disponível em algumas regiões - veja a disponibilidade atual de Armazenamento de [Data Lake](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Localização dos dados existentes

Se já tem uma conta de armazenamento ou armazenamento de data lake contendo os seus dados e pretende utilizar este armazenamento como armazenamento padrão do seu cluster, então deve implementar o seu cluster no mesmo local.

### <a name="storage-size"></a>Tamanho do armazenamento

Depois de ter um cluster HDInsight implantado, pode anexar contas adicionais de Armazenamento Azure ou aceder a outros Armazenamentos de Data Lake. Todas as suas contas de armazenamento devem residir no mesmo local que o seu cluster. Um Armazenamento de Data Lake pode estar em um local diferente, embora isso possa introduzir alguns dados leitura/escrita latência.

O Armazenamento Azure tem [alguns limites](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)de capacidade, enquanto data Lake Storage Gen1 é virtualmente ilimitado.

Um cluster pode aceder a uma combinação de diferentes contas de armazenamento. Exemplos típicos incluem:

* Quando a quantidade de dados é suscetível de exceder a capacidade de armazenamento de um único recipiente de armazenamento de bolhas.
* Quando a taxa de acesso ao recipiente de bolhas pode exceder o limiar em que ocorre a aceleração.
* Quando pretende fazer dados, já carregou para um recipiente de bolhas à disposição do cluster.
* Quando se pretende isolar diferentes partes do armazenamento por razões de segurança, ou simplificar a administração.

Para um melhor desempenho, utilize apenas um recipiente por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolha um tipo de cluster

O tipo de cluster determina a carga de trabalho que o seu cluster HDInsight está configurado para funcionar, tais como [Apache Hadoop,](https://hadoop.apache.org/) [Apache Storm,](https://storm.apache.org/) [Apache Kafka,](https://kafka.apache.org/)ou [Apache Spark](https://spark.apache.org/). Para uma descrição detalhada dos tipos de cluster disponíveis, consulte [introdução ao Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster tem uma topologia de implantação específica que inclui requisitos para o tamanho e número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolha o tamanho e o tipo VM

Cada tipo de cluster tem um conjunto de tipos de nó, e cada tipo de nó tem opções específicas para o seu tamanho e tipo vm.

Para determinar o tamanho ideal do cluster para a sua aplicação, pode comparar a capacidade do cluster e aumentar o tamanho indicado. Por exemplo, pode utilizar uma carga de trabalho simulada ou uma *consulta canária*. Com uma carga de trabalho simulada, executa as suas cargas de trabalho esperadas em diferentes clusters de tamanhos, aumentando gradualmente o tamanho até que o desempenho desejado seja atingido. Uma consulta canária pode ser inserida periodicamente entre as outras consultas de produção para mostrar se o cluster tem recursos suficientes.

Para obter mais informações sobre como escolher a família VM certa para a sua carga de trabalho, consulte [Selecionando o tamanho de VM certo para o seu cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Escolha a escala de cluster

A escala de um cluster é determinada pela quantidade dos seus nós VM. Para todos os tipos de cluster, existem tipos de nó que têm uma escala específica, e tipos de nó que suportam a escala. Por exemplo, um aglomerado pode exigir exatamente três nós [apache zookeeper](https://zookeeper.apache.org/) ou dois nós de cabeça. Os nós dos trabalhadores que fazem o processamento de dados de forma distribuída podem beneficiar da escala, adicionando nós de trabalhadores adicionais.

Dependendo do seu tipo de cluster, o aumento do número de nós de trabalhador adiciona capacidade computacional adicional (como mais núcleos), mas também pode adicionar à quantidade total de memória necessária para que todo o cluster suporte o armazenamento em memória dos dados que estão a ser processados. Tal como acontece com a escolha do tamanho e tipo vm, a seleção da balança de cluster sucuviadamente é alcançada empiricamente, utilizando cargas de trabalho simuladas ou consultas canárias.

Pode escalar o seu cluster para satisfazer as exigências de carga máxima, e depois escaloná-lo de volta quando esses nós extras já não forem necessários. A [função De escala Automática](hdinsight-autoscale-clusters.md) permite-lhe escalar automaticamente o seu cluster com base em métricas e timings pré-determinados. Para obter mais informações sobre a escala dos seus clusters manualmente, consulte [os clusters Scale HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

És acusado pela vida de um aglomerado. Se houver apenas momentos específicos em que precisa do seu cluster em funcionamento, pode [criar clusters a pedido utilizando](hdinsight-hadoop-create-linux-clusters-adf.md)a Azure Data Factory . Também pode criar scripts PowerShell que disponibilizem e apaguem o seu cluster e, em seguida, programar esses scripts usando [a Automação Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando um cluster é eliminado, a sua metaloja por defeito da Hive também é eliminada. Para persistir a metaloja para a recriação do cluster seguinte, utilize uma loja de metadados externo, como a Base de Dados Azure ou [a Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar erros de trabalho de cluster

Por vezes, erros podem ocorrer devido à execução paralela de vários mapas e reduzir componentes num cluster de vários nós. Para ajudar a isolar a questão, tente testar distribuídos executando múltiplos empregos simultâneos num único aglomerado de nó de trabalhadores, em seguida, expandir esta abordagem para executar múltiplos empregos simultaneamente em clusters que contenham mais do que um nó. Para criar um cluster HDInsight de nó único em Azure, use a opção *Custom (tamanho,configurações, apps)* e use um valor de 1 para *número de nós de trabalhador* na secção tamanho do **Cluster** ao fornecer um novo cluster no portal.

## <a name="quotas"></a>Quotas

Depois de determinar o tamanho, escala e tipo de vm do seu cluster-alvo, verifique os limites de capacidade de quota atuais da sua subscrição. Quando se atinge um limite de quota, pode não ser capaz de implantar novos clusters, ou de eliminar os aglomerados existentes adicionando mais nós de trabalhadores. O único limite de quota é a quota CPU Cores que existe ao nível da região para cada subscrição. Por exemplo, a sua subscrição pode ter um limite de 30 núcleos na região leste dos EUA. 

Para verificar os seus núcleos disponíveis, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para a página **de visão geral** do cluster HDInsight. 
3. No menu esquerdo, clique nos **limites**de quota .

   A página mostra o número de núcleos em uso, o número de núcleos disponíveis e os núcleos totais.

Se necessitar de solicitar um aumento de quota, faça os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Ajuda + suporte** no lado inferior esquerdo da página.
1. Selecione **Novo pedido de suporte**.
1. Na página de pedido de **suporte Novo,** sob o separador **Basics,** selecione as seguintes opções:

   - **Tipo de emissão**: Limites de **serviço e subscrição (quotas)**
   - **Subscrição**: a subscrição que pretende modificar
   - **Tipo**de quota : **HDInsight**

     ![Criar um pedido de suporte para aumentar a quota central do HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecione **Seguinte: Soluções >>** .
1. Na página **Detalhes,** introduza uma descrição do problema, selecione a gravidade do problema, o seu método de contacto preferido e outros campos necessários.
1. Selecione **Seguinte: Rever + criar >>** .
1. No **separador Review + criar,** selecione **Criar**.

> [!NOTE]  
> Se precisar de aumentar a quota central do HDInsight numa região privada, [apresente um pedido de whitelist](https://aka.ms/canaryintwhitelist).

Pode [contactar o suporte para solicitar um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)de quota.

No entanto, existem alguns limites de quota fixa, por exemplo, uma única subscrição azure pode ter no máximo 10.000 núcleos. Para mais detalhes sobre estes limites, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

## <a name="next-steps"></a>Passos seguintes

* [Configure clusters em HDInsight com Apache Hadoop, Spark, Kafka e muito mais:](hdinsight-hadoop-provision-linux-clusters.md)Saiba como configurar e configurar clusters em HDInsight com Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services ou Storm.
* [Monitorize](hdinsight-key-scenarios-to-monitor.md)o desempenho do cluster : Saiba sobre cenários-chave para monitorizar o seu cluster HDInsight que possam afetar a capacidade do seu cluster.
