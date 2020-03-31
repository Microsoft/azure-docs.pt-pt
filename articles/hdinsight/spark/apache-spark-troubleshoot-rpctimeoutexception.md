---
title: RpcTimeoutException for Apache Spark thrift - Azure HDInsight
description: Você vê 502 erros ao processar grandes conjuntos de dados usando o servidor de thrift Apache Spark
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894271"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Cenário: RpcTimeoutException para o servidor de poupança Apache Spark no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

A aplicação spark `org.apache.spark.rpc.RpcTimeoutException` falha com `Futures timed out`uma exceção e uma mensagem: , como no seguinte exemplo:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`e `overhead limit exceeded` podem também aparecer `sparkthriftdriver.log` erros no seguinte exemplo:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Estes erros são causados pela falta de recursos de memória durante o processamento de dados. Se o processo de recolha de lixo de Java começar, pode levar ao enforcamento da aplicação Spark. As consultas começarão a esgotar-se e a parar de processar. O `Futures timed out` erro indica um aglomerado sob stress severo.

## <a name="resolution"></a>Resolução

Aumente o tamanho do cluster adicionando mais nós de trabalhador ou aumentando a capacidade de memória dos nós de cluster existentes. Também pode ajustar o pipeline de dados para reduzir a quantidade de dados que estão a ser processados de uma só vez.

O `spark.network.timeout` tempo limite para todas as ligações de rede. O aumento do tempo limite de rede pode dar mais tempo para que algumas operações críticas terminem, mas isso não resolverá completamente o problema.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
