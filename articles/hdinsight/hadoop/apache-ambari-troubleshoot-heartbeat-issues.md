---
title: Problemas de heartbeat do Apache Ambari no Azure HDInsight
description: Revisão de várias razões para problemas cardíacos apache Ambari em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 5eebde42098d74f533565d274b693c4a06f2f60d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946731"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemas de heartbeat do Apache Ambari no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Cenário: Alta utilização do CPU

### <a name="issue"></a>Problema

O agente Ambari tem alta utilização do CPU, o que resulta em alertas da UI de Ambari que para alguns nós o batimento cardíaco do agente Ambari está perdido. O alerta perdido do batimento cardíaco é geralmente transitório.

### <a name="cause"></a>Causa

Devido a vários bugs ambari-agent, em casos raros, o seu agente ambari pode ter uma utilização de CPU de alta percentagem (perto de 100).

### <a name="resolution"></a>Resolução

1. Identificar iD (pid) do agente ambari:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Em seguida, execute o seguinte comando para mostrar a utilização da CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Reinicie o agente ambari para atenuar a questão:

    ```bash
    service ambari-agent restart
    ```

1. Se o reinício não funcionar, mate o processo de agente ambari e, em seguida, inicie-o:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Cenário: Agente Ambari ainda não começou

### <a name="issue"></a>Problema

O agente Ambari ainda não começou o que resulta em alertas da UI de Ambari que para alguns nós o batimento cardíaco do agente Ambari está perdido.

### <a name="cause"></a>Causa

Os alertas são causados pelo agente Ambari que não está a funcionar.

### <a name="resolution"></a>Resolução

1. Confirmar o estado do agente ambari:

    ```bash
    service ambari-agent status
    ```

1. Confirme se os serviços de controlo de falhas estão a funcionar:

    ```bash
    ps -ef | grep failover
    ```

    Se os serviços de controlo de falha não estiverem a funcionar, é provável que seja devido a um problema que impeça o hdinsight-agente de iniciar o controlador de falha. Verifique o registo do agente hdinsight a partir do `/var/log/hdinsight-agent/hdinsight-agent.out` ficheiro.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Cenário: Batimento cardíaco perdido para Ambari

### <a name="issue"></a>Problema

O agente cardíaco de Ambari perdeu-se.

### <a name="cause"></a>Causa

Os registos OMS estão a causar uma elevada utilização do CPU.

### <a name="resolution"></a>Resolução

* Desativar o registo do Monitor Azure utilizando o cmdlet PowerShell [desactivado-AzHDInsightMonitoring.](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)
* Eliminar o `mdsd.warn` ficheiro de registo

---

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]