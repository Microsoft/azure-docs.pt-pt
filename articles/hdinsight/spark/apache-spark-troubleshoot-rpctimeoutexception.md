---
title: RpcTimeoutException para Apache Spark thrift - Azure HDInsight
description: Você vê 502 erros ao processar grandes conjuntos de dados usando o servidor Apache Spark thrift
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: a29d36c5ba6fdd51de27afa3ab4dfe1258332200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208421"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para apache spark thrift servidor em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

A aplicação Spark falha com uma `org.apache.spark.rpc.RpcTimeoutException` exceção e uma mensagem: `Futures timed out` , como no exemplo seguinte:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` e `overhead limit exceeded` podem igualmente aparecer erros no `sparkthriftdriver.log` exemplo seguinte:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Estes erros são causados pela falta de recursos de memória durante o processamento de dados. Se o processo de recolha de lixo java começar, pode levar à aplicação Spark para parar de responder. As consultas começarão a esgotar-se e a parar o processamento. O `Futures timed out` erro indica um aglomerado sob forte tensão.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós operários ou aumentando a capacidade de memória dos nós de cluster existentes. Também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão a ser tratados de uma só vez.

Controla `spark.network.timeout` o tempo limite para todas as ligações de rede. Aumentar o tempo limite de rede pode dar mais tempo para algumas operações críticas terminarem, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
