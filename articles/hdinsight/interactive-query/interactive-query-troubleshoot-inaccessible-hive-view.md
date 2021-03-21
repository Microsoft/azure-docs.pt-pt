---
title: Ligações Apache Hive ao Apache Zookeeper - Azure HDInsight
description: Apache Hive View inacessível devido a problemas do Zookeeper Apache em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939310"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Cenário: A Colmeia Apache não estabelece uma ligação ao Zookeeper Apache em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

A Vista de Colmeia é inacessível, e os registos `/var/log/hive` mostram um erro semelhante ao seguinte:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Causa

É possível que a Colmeia não consiga estabelecer uma ligação com o Zookeeper, o que impede o lançamento da Hive View.

## <a name="resolution"></a>Resolução

1. Verifique se o serviço zookeeper está saudável.

1. Verifique se o serviço Zookeeper tem uma entrada ZNode para Hive Server2. O valor será em falta ou incorreto.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Para restabelecer a conectividade, reinicie os nós do Zookeeper e reinicie o HiveServer2.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]