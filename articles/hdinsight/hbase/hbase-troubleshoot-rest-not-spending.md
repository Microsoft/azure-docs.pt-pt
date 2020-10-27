---
title: Apache HBase REST não respondendo aos pedidos em Azure HDInsight
description: Resolver problema com Apache HBase REST não respondendo aos pedidos em Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 9188dc43f6d4a49dd0ddee65693dbd83daa6f1f2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540028"
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

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).