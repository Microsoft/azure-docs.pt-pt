---
title: Baixar problemas usando JDBC/ODBC e o Apache Thrift Framework-Azure HDInsight
description: Não é possível baixar conjuntos de dados grandes usando JDBC/ODBC e a estrutura de software Apache Thrift no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 4c1e16666bc5655ccf62522c5f860051adbdeda1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885090"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Não é possível baixar conjuntos de dados grandes usando JDBC/ODBC e a estrutura de software Apache Thrift no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar baixar grandes conjuntos de dados usando JDBC/ODBC e a estrutura de software Apache Thrift no Azure HDInsight, você receberá uma mensagem de erro semelhante à seguinte:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Essa exceção é causada pelo processo de serialização que está tentando usar mais espaço de buffer do que é permitido. No Spark 2.0.0, a classe `org.apache.spark.serializer.KryoSerializer` é usada para serializar objetos quando os dados são acessados por meio da estrutura de software Apache thrift. Uma classe diferente é usada para dados que serão enviados pela rede ou armazenados em cache na forma serializada.

## <a name="resolution"></a>Resolução

Aumente o `Kryoserializer` valor do buffer. Adicione uma chave chamada `spark.kryoserializer.buffer.max` e defina-a `2048` como em spark2 config `Custom spark2-thrift-sparkconf`em.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
