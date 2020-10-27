---
title: Nó de cluster fica sem espaço em disco em Azure HDInsight
description: Problemas na resolução de problemas problemas do espaço do disco do cluster Apache Hadoop em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: 042a894d9f6f2a8fb7737d0a20abeb2293f756aa
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540606"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Cenário: Nó de cluster fica sem espaço em disco em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Um trabalho pode falhar com uma mensagem de erro semelhante a: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Ou pode receber o alerta Apache Ambari semelhante a: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Causa

A cache da aplicação Apache Yarn pode ter consumido todo o espaço disponível em disco. A sua aplicação Spark está provavelmente a funcionar de forma ineficiente.

## <a name="resolution"></a>Resolução

1. Utilize a UI Ambari para determinar que nó está a ficar sem espaço em disco.

1. Determine qual pasta no nó perturbador contribui para a maior parte do espaço do disco. SSH para o nó primeiro, em seguida, correr `df` para listar a utilização do disco para todos os suportes. Normalmente é `/mnt` um disco temporário usado pela OSS. Pode introduzir uma pasta e, em seguida, escrever `sudo du -hs` para mostrar os tamanhos de ficheiro sumários debaixo de uma pasta. Se vir uma pasta semelhante a `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , isto significa que a aplicação ainda está em execução. Isto pode ser devido à persistência de RDD ou a ficheiros de baralhar intermédios.

1. Para mitigar o problema, desligue a aplicação, que libertará o espaço do disco utilizado por essa aplicação.

1. Se o problema acontecer frequentemente nos nós dos trabalhadores, pode sintonizar as definições de cache locais yarn no cluster.

    Abra a Ambari UI Navigate para YARN -- > Configs -- > Advanced.  
    Adicione as seguintes 2 propriedades à secção de yarn-site.xml personalizado e guarde:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Se o acima referido não corrigir permanentemente o problema, otimize a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).