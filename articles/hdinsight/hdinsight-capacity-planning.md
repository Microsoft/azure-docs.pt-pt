---
title: Planejamento de capacidade de cluster no Azure HDInsight
description: Identifique as principais perguntas para a capacidade e o planejamento de desempenho de um cluster do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 31d7299b0c14b35aad7a29c875630d8a7fa4827e
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200599"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planejamento de capacidade para clusters HDInsight

Antes de implantar um cluster HDInsight, planeje a capacidade de cluster desejada determinando o desempenho e a escala necessários. Este planejamento ajuda a otimizar a usabilidade e os custos. Algumas decisões de capacidade de cluster não podem ser alteradas após a implantação. Se os parâmetros de desempenho forem alterados, um cluster poderá ser desmontado e recriado sem perder os dados armazenados.

As principais perguntas a serem feitas para o planejamento de capacidade são:

* Em qual região geográfica você deve implantar o cluster?
* Quanto armazenamento você precisa?
* Qual tipo de cluster você deve implantar?
* Qual tamanho e tipo de VM (máquina virtual) os nós de cluster devem usar?
* Quantos nós de trabalho seu cluster deve ter?

## <a name="choose-an-azure-region"></a>Escolher uma região do Azure

A região do Azure determina onde o cluster está fisicamente provisionado. Para minimizar a latência de leituras e gravações, o cluster deve estar próximo dos dados.

O HDInsight está disponível em muitas regiões do Azure. Para localizar a região mais próxima, consulte [produtos disponíveis por região](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=hdinsight/).

## <a name="choose-storage-location-and-size"></a>Escolher o local e o tamanho do armazenamento

### <a name="location-of-default-storage"></a>Local do armazenamento padrão

O armazenamento padrão, ou seja, uma conta de armazenamento do Azure ou Azure Data Lake Storage, deve estar no mesmo local que o cluster. O armazenamento do Azure está disponível em todos os locais. Data Lake Storage Gen1 está disponível em algumas regiões – consulte a atual [Data Lake Storage disponibilidade](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Local dos dados existentes

Se você já tiver uma conta de armazenamento ou Data Lake Storage que contenha seus dados e quiser usar esse armazenamento como o armazenamento padrão do cluster, será necessário implantar o cluster no mesmo local.

### <a name="storage-size"></a>Tamanho do armazenamento

Depois de ter um cluster HDInsight implantado, você pode anexar outras contas de armazenamento do Azure ou acessar outros Data Lake Storage. Todas as suas contas de armazenamento devem residir no mesmo local que o cluster. Um Data Lake Storage pode estar em um local diferente, embora isso possa introduzir uma latência de leitura/gravação de dados.

O armazenamento do Azure tem alguns [limites de capacidade](../azure-subscription-service-limits.md#storage-limits), enquanto data Lake Storage Gen1 é praticamente ilimitado.

Um cluster pode acessar uma combinação de diferentes contas de armazenamento. Os exemplos típicos incluem:

* Quando a quantidade de dados provavelmente exceder a capacidade de armazenamento de um único contêiner de armazenamento de BLOBs.
* Quando a taxa de acesso ao contêiner de BLOBs pode exceder o limite em que ocorre a limitação.
* Quando desejar tornar os dados, você já carregou em um contêiner de blob disponível para o cluster.
* Quando você quiser isolar partes diferentes do armazenamento por motivos de segurança ou simplificar a administração.

Para um cluster de 48 nós, recomendamos de 4 a 8 contas de armazenamento. Embora possa já haver um armazenamento total suficiente, cada conta de armazenamento fornece largura de banda de rede adicional para os nós de computação. Quando você tiver várias contas de armazenamento, use um nome aleatório para cada conta de armazenamento, sem um prefixo. A finalidade da nomenclatura aleatória é reduzir a chance de gargalos de armazenamento (limitação) ou falhas de modo comum em todas as contas. Para obter um melhor desempenho, use apenas um contêiner por conta de armazenamento.

## <a name="choose-a-cluster-type"></a>Escolher um tipo de cluster

O tipo de cluster determina a carga de trabalho que o cluster HDInsight está configurado para executar, como [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)ou [Apache Spark](https://spark.apache.org/). Para obter uma descrição detalhada dos tipos de cluster disponíveis, consulte [introdução ao Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster tem uma topologia de implantação específica que inclui requisitos para o tamanho e o número de nós.

## <a name="choose-the-vm-size-and-type"></a>Escolha o tamanho e o tipo da VM

Cada tipo de cluster tem um conjunto de tipos de nós e cada tipo de nó tem opções específicas para o tamanho e o tipo da VM.

Para determinar o tamanho de cluster ideal para seu aplicativo, você pode avaliar a capacidade do cluster e aumentar o tamanho conforme indicado. Por exemplo, você pode usar uma carga de trabalho simulada ou uma *consulta canário*. Com uma carga de trabalho simulada, você executa suas cargas de trabalho esperadas em diferentes clusters de tamanho, aumentando gradualmente o tamanho até que o desempenho desejado seja atingido. Uma consulta canário pode ser inserida periodicamente entre as outras consultas de produção para mostrar se o cluster tem recursos suficientes.

Para obter mais informações sobre como escolher a família de VMs correta para sua carga de trabalho, consulte [selecionando o tamanho correto da VM para o cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Escolher a escala do cluster

A escala de um cluster é determinada pela quantidade de seus nós de VM. Para todos os tipos de cluster, há tipos de nós que têm uma escala específica e tipos de nós que dão suporte à expansão. Por exemplo, um cluster pode exigir exatamente três nós [Apache ZooKeeper](https://zookeeper.apache.org/) ou dois nós de cabeçalho. Os nós de trabalho que fazem o processamento de dados de maneira distribuída podem se beneficiar do dimensionamento, adicionando mais nós de trabalho.

Dependendo do tipo de cluster, o aumento do número de nós de trabalho adiciona capacidade computacional adicional (como mais núcleos), mas também pode ser adicionado à quantidade total de memória necessária para que todo o cluster dê suporte ao armazenamento na memória dos dados que estão sendo processados. Assim como acontece com a escolha do tamanho e do tipo da VM, selecionar a escala de cluster correta normalmente é atingido empiricamente, usando cargas de trabalho simuladas ou consultas canário.

Você pode escalar horizontalmente o cluster para atender às demandas de pico de carga e, em seguida, dimensioná-lo novamente quando esses nós extras não forem mais necessários. O [recurso de dimensionamento automático](hdinsight-autoscale-clusters.md) permite dimensionar automaticamente o cluster com base nas métricas e nos tempos predeterminados. Para obter mais informações sobre como dimensionar seus clusters manualmente, consulte [dimensionar clusters HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida do cluster

Você é cobrado pelo tempo de vida de um cluster. Se houver apenas alguns momentos específicos em que você precisa de seu cluster em funcionamento, você pode [Criar clusters sob demanda usando Azure data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Você também pode criar scripts do PowerShell que provisionam e excluam seu cluster e, em seguida, agendar esses scripts usando a [automação do Azure](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Quando um cluster é excluído, seu metastore do Hive padrão também é excluído. Para persistir o metastore para a próxima recriação de cluster, use um repositório de metadados externo, como o banco de dados do Azure ou o [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolar erros de trabalho de cluster

Às vezes, podem ocorrer erros devido à execução paralela de vários mapas e à redução de componentes em um cluster de vários nós. Para ajudar a isolar o problema, experimente o teste distribuído executando vários trabalhos simultâneos em um único cluster de nó de trabalho e, em seguida, expanda essa abordagem para executar vários trabalhos simultaneamente em clusters que contenham mais de um nó. Para criar um cluster HDInsight de nó único no Azure, use a opção *personalizado (tamanho, configurações, aplicativos)* e use um valor de 1 para o *número de nós de trabalho* na seção **tamanho do cluster** ao provisionar um novo cluster no Portal.

## <a name="quotas"></a>Quotas

Depois de determinar o tamanho, a escala e o tipo da VM do cluster de destino, verifique os limites de capacidade da cota atual da sua assinatura. Quando você atingir um limite de cota, talvez não seja possível implantar novos clusters ou escalar horizontalmente os clusters existentes adicionando mais nós de trabalho. O único limite de cota é a cota de núcleos de CPU que existe no nível de região para cada assinatura. Por exemplo, sua assinatura pode ter um limite de 30 núcleos na região leste dos EUA. Se você precisar solicitar um aumento de cota, execute as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **ajuda + suporte** no lado inferior esquerdo da página.
1. Selecione **Novo pedido de suporte**.
1. Na página **nova solicitação de suporte** , na guia **noções básicas** , selecione as seguintes opções:

   - **Tipo de problema**: **limites de serviço e assinatura (cotas)**
   - **Assinatura**: a assinatura que você deseja modificar
   - **Tipo de cota**: **HDInsight**

     ![Criar uma solicitação de suporte para aumentar a cota de núcleo do HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecione **Avançar: soluções > >** .
1. Na página **detalhes** , insira uma descrição do problema, selecione a severidade do problema, seu método de contato preferencial e outros campos obrigatórios.
1. Selecione **Avançar: revisar + criar > >** .
1. Na guia **revisar + criar** , selecione **criar**.

> [!NOTE]  
> Se você precisar aumentar a cota de núcleo do HDInsight em uma região privada, [envie uma solicitação de lista de](https://aka.ms/canaryintwhitelist)permissões.

Você pode [entrar em contato com o suporte para solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

No entanto, há alguns limites de cota fixos, por exemplo, uma única assinatura do Azure pode ter no máximo 10.000 núcleos. Para obter detalhes sobre esses limites, consulte [assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Passos seguintes

* [Configure clusters no hdinsight com Apache Hadoop, Spark, Kafka e muito mais](hdinsight-hadoop-provision-linux-clusters.md): saiba como configurar e configurar clusters no HDInsight com Apache Hadoop, Spark, Kafka, Hive interativo, HBase, serviços de ml ou Storm.
* [Monitorar o desempenho do cluster](hdinsight-key-scenarios-to-monitor.md): Saiba mais sobre os principais cenários para monitorar o cluster HDInsight que pode afetar a capacidade do cluster.
