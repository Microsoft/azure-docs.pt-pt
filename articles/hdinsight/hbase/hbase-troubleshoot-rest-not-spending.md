---
title: Apache HBase REST não responde a pedidos no Azure HDInsight
description: Resolver problema com apache HBase REST não respondendo aos pedidos no Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887177"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Cenário: Apache HBase REST não responde a pedidos no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

O serviço Apache HBase REST não responde aos pedidos no Azure HDInsight.

## <a name="cause"></a>Causa

A causa possível aqui pode ser o serviço Apache HBase REST são as tomadas de fuga, o que é especialmente comum quando o serviço está em funcionamento há muito tempo (por exemplo, meses). A partir do SDK cliente, pode ver uma mensagem de erro semelhante a:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Resolução

Reiniciar o HBase REST utilizando o comando abaixo após o SSHing ao hospedeiro. Também pode usar ações de script para reiniciar este serviço em todos os nós dos trabalhadores:

```bash
sudo service hdinsight-hbrest restart
```

Este comando irá parar o Servidor da Região HBase no mesmo hospedeiro. Pode iniciar manualmente o Servidor da Região HBase através de Ambari, ou deixar a funcionalidade de reinício automático da Ambari recuperar automaticamente o Servidor da Região HBase.

Se o problema persistir, pode instalar o seguinte guião de mitigação como um trabalho de CRON que funciona a cada 5 minutos em cada nó de trabalhador. Este roteiro de mitigação pings o serviço REST e reinicia-o caso o serviço REST não responda.

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

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
