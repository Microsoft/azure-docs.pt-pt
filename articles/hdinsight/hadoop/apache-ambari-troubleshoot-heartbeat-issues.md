---
title: Problemas de pulsação do Apache Ambari no Azure HDInsight
description: Vários motivos para problemas de pulsação do Apache Ambari no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: a74e826e9a4725c9b689dc97101b00615771d4d1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781403"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de pulsação do Apache Ambari no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: Utilização elevada de CPU

### <a name="issue"></a>Problema

O agente Ambari tem alta utilização da CPU, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida.

### <a name="cause"></a>Causa

Devido a vários bugs do ambari Agent, em casos raros, o ambari-Agent pode ter alta utilização de CPU de percentual (perto de 100).

### <a name="resolution"></a>Resolução

1. Identificar a ID do processo (PID) do ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reinicie o ambari-Agent para atenuar o problema:

    ```bash
    service ambari-agent restart
    ```

1. Se a reinicialização não funcionar, encerre o processo do ambari e inicie-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: Agente Ambari não iniciado

### <a name="issue"></a>Problema

O agente Ambari não foi iniciado, o que resulta em alertas da interface do usuário do Ambari que, para alguns nós, a pulsação do agente do Ambari é perdida.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente do Ambari não estar em execução.

### <a name="resolution"></a>Resolução

1. Confirme o status do ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços do controlador de failover estão em execução:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços do controlador de failover não estiverem em execução, provavelmente devido a um problema, impeça que o hdinsight-Agent inicie o controlador de failover. Verifique o log do hdinsight- `/var/log/hdinsight-agent/hdinsight-agent.out` Agent do arquivo.

---

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
