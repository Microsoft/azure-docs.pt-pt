---
title: Apache HBase REST não respondendo aos pedidos em Azure HDInsight
description: Resolver problema com Apache HBase REST não respondendo aos pedidos em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286967"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Cenário: Apache HBase REST não responde aos pedidos em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O serviço Apache HBase REST não responde aos pedidos no Azure HDInsight.

## <a name="cause"></a>Causa

A causa possível aqui pode ser o serviço Apache HBase REST está a vazar tomadas, o que é especialmente comum quando o serviço está em funcionamento há muito tempo (por exemplo, meses). A partir do cliente SDK, poderá ver uma mensagem de erro semelhante a:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Resolução

Reinicie o HBase REST utilizando o comando abaixo após sSHing para o anfitrião. Também pode utilizar ações de script para reiniciar este serviço em todos os nós dos trabalhadores:

```bash
sudo service hdinsight-hbrest restart
```

Este comando irá parar o Servidor da Região HBase no mesmo anfitrião. Pode iniciar manualmente o HBase Region Server através de Ambari ou deixar a Ambari reiniciar automaticamente a funcionalidade HBase Region Server.

Se o problema persistir, pode instalar o seguinte script de mitigação como um trabalho cron que funciona a cada 5 minutos em cada nó de trabalhador. Este script de mitigação pings o serviço REST e reinicia-o no caso do serviço REST não responder.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]