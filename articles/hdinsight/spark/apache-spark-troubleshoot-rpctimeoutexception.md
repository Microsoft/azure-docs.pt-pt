---
title: RpcTimeoutException para o servidor de Apache Spark Thrift no Azure HDInsight
description: Você verá erros 502 ao processar grandes conjuntos de dados usando Apache Spark servidor thrift
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 220465105733b715c1990fd73904b9dc095637d4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088604"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para o servidor de Apache Spark Thrift no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O aplicativo Spark falha com `org.apache.spark.rpc.RpcTimeoutException` uma exceção e uma mensagem `Futures timed out`:, como no exemplo a seguir:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`e `overhead limit exceeded` os erros também podem aparecer `sparkthriftdriver.log` no como no exemplo a seguir:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Esses erros são causados por uma falta de recursos de memória durante o processamento de dados. Se o processo de coleta de lixo do Java for iniciado, ele poderá levar à suspensão do aplicativo Spark. As consultas começarão a atingir o tempo limite e interromperão o processamento. O `Futures timed out` erro indica um cluster sob sobrecarga grave.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalho ou aumentando a capacidade de memória dos nós de cluster existentes. Você também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão sendo processados de uma vez.

O `spark.network.timeout` controla o tempo limite para todas as conexões de rede. Aumentar o tempo limite da rede pode permitir mais tempo para que algumas operações críticas sejam concluídas, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
