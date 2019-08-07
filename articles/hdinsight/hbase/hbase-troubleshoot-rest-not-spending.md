---
title: O Apache HBase REST não está respondendo a solicitações no Azure HDInsight
description: Resolver o problema com o HDInsight HBase REST não respondendo a solicitações
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 7219f66457e47bba34e750ec74810b8d2edee36e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817111"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Cenário: O Apache HBase REST não está respondendo a solicitações no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O serviço REST do Apache HBase não responde às solicitações no Azure HDInsight.

## <a name="cause"></a>Causa

A causa possível aqui pode ser o serviço REST do Apache HBase está vazando soquetes, o que é especialmente comum quando o serviço está em execução por um longo período (por exemplo, meses). No SDK do cliente, você pode ver uma mensagem de erro semelhante a:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Resolução

Reinicie o HBase REST usando o comando abaixo após SSHing para o host. Você também pode usar ações de script para reiniciar esse serviço em todos os nós de trabalho:

```bash
sudo service hdinsight-hbrest restart
```

Esse comando irá parar o servidor de região do HBase no mesmo host. Você pode iniciar manualmente o servidor de região do HBase por meio de Ambari ou deixar a funcionalidade de reinicialização automática do Ambari recuperar o servidor de região do HBase automaticamente.

Se o problema ainda persistir, você poderá instalar o script de mitigação a seguir como um trabalho CRON que é executado a cada 5 minutos em cada nó de trabalho. Esse script de mitigação executa ping no serviço REST e o reinicia caso o serviço REST não responda.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
