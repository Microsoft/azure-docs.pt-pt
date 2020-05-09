---
title: O nó de cluster fica sem espaço de disco no Azure HDInsight
description: Problemas de resolução de problemas Apache Hadoop problemas de espaço em disco de cluster em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: ead79ca0a37a270f03a305064c80426553db59ca
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628542"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Cenário: Nó de cluster fica sem espaço de disco no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Um trabalho pode falhar com uma mensagem de erro semelhante a:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Ou pode receber alerta Apache Ambari semelhante a: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Causa

A cache da aplicação Apache Yarn pode ter consumido todo o espaço disponível do disco. A sua aplicação Spark está provavelmente a funcionar de forma ineficiente.

## <a name="resolution"></a>Resolução

1. Use Ambari UI para determinar que nó está ficando sem espaço de disco.

1. Determinar qual a pasta no nó perturbador contribui para a maior parte do espaço do disco. SSH para o nó primeiro, em seguida, correr `df` para listar o uso do disco para todos os suportes. Normalmente `/mnt` é um disco temporário usado pela OSS. Pode introduzir uma pasta e, em seguida, digitar `sudo du -hs` para mostrar tamanhos de ficheiro resumidos sob uma pasta. Se vir uma pasta `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`semelhante à, isto significa que a aplicação ainda está em execução. Isto pode ser devido à persistência de RDD ou ficheiros intermédios de baralhar.

1. Para mitigar o problema, mate a aplicação, que libertará o espaço do disco utilizado por essa aplicação.

1. Se o problema ocorrer frequentemente nos nós dos trabalhadores, pode sintonizar as definições de cache locais yARN no cluster.

    Abra a Ambari UI Navigate para o YARN --> Configs --> Advanced.  
    Adicione as seguintes 2 propriedades à secção personalizado de fios-site.xml e guarde:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Se o que precede não corrigir permanentemente o problema, otimize a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
