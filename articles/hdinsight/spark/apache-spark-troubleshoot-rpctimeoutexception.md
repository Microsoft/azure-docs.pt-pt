---
title: RpcTimeoutException para Apache Spark Thrift-Azure HDInsight
description: Você verá erros 502 ao processar grandes conjuntos de dados usando Apache Spark servidor thrift
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894271"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para o servidor de Apache Spark Thrift no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Spark falha com uma exceção `org.apache.spark.rpc.RpcTimeoutException` e uma mensagem: `Futures timed out`, como no exemplo a seguir:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

os erros de `OutOfMemoryError` e `overhead limit exceeded` também podem aparecer no `sparkthriftdriver.log` como no exemplo a seguir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Esses erros são causados por uma falta de recursos de memória durante o processamento de dados. Se o processo de coleta de lixo do Java for iniciado, ele poderá levar à suspensão do aplicativo Spark. As consultas começarão a atingir o tempo limite e interromperão o processamento. O erro de `Futures timed out` indica um cluster sob estresse severo.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalho ou aumentando a capacidade de memória dos nós de cluster existentes. Você também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão sendo processados de uma vez.

O `spark.network.timeout` controla o tempo limite para todas as conexões de rede. Aumentar o tempo limite da rede pode permitir mais tempo para que algumas operações críticas sejam concluídas, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
