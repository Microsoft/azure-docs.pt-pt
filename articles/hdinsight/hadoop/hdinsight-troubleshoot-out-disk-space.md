---
title: Nó de cluster fica sem espaço em disco em Azure HDInsight
description: Problemas na resolução de problemas problemas do espaço do disco do cluster Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: c58ac1e65509299680f67cf17060ff3e9af4c817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944364"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]