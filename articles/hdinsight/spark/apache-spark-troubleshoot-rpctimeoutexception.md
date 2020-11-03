---
title: RpcTimeoutException para Apache Spark thrift - Azure HDInsight
description: Você vê 502 erros ao processar grandes conjuntos de dados usando o servidor Apache Spark thrift
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b4b5268bef71bd6b23df6049fd3ac8af973d4385
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287750"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]