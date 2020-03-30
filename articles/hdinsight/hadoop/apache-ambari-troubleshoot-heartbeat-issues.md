---
title: Problemas de heartbeat do Apache Ambari no Azure HDInsight
description: Revisão de várias razões para problemas cardíacos de Apache Ambari no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057078"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de heartbeat do Apache Ambari no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: Alta utilização do CPU

### <a name="issue"></a>Problema

O agente Ambari tem uma alta utilização de CPU, o que resulta em alertas da Ambari UI que para alguns nós o batimento cardíaco do agente Ambari está perdido. O alerta de batimentocardíaco perdido é geralmente transitório.

### <a name="cause"></a>Causa

Devido a vários bugs de agente ambari, em casos raros, o seu agente ambari pode ter uma utilização alta (perto de 100) percentual do CPU.

### <a name="resolution"></a>Resolução

1. Identificar o ID do processo (pid) do agente ambari:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reiniciar o ambari-agent para atenuar a questão:

    ```bash
    service ambari-agent restart
    ```

1. Se o reinício não funcionar, mate o processo do agente ambari e, em seguida, ligue-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: Agente ambari não começou

### <a name="issue"></a>Problema

O agente Ambari ainda não começou o que resulta em alertas da Ambari UI que, para alguns nós, o batimento cardíaco do agente Ambari está perdido.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente Ambari não estar a funcionar.

### <a name="resolution"></a>Resolução

1. Confirmar o estado do agente ambari:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços de controlador failover estão em execução:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços de controlador failover não estiverem em funcionamento, é provável que seja devido a um problema impedir que o agente hdinsight-agent inicie o controlador de failover. Verifique o registo do `/var/log/hdinsight-agent/hdinsight-agent.out` agente hdinsight a partir do ficheiro.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Cenário: Batimento cardíaco perdido para Ambari

### <a name="issue"></a>Problema

O agente do batimento cardíaco ambari perdeu-se.

### <a name="cause"></a>Causa

Os registos oMS estão a causar uma alta utilização do CPU.

### <a name="resolution"></a>Resolução

* Desativar a exploração do Monitor Azure utilizando o cmdlet PowerShell [demonitorização de desativação AzHDInsightMonitoring.](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)
* Eliminar `mdsd.warn` o ficheiro de registo

---

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
