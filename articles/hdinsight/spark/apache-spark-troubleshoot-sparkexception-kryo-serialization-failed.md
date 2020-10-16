---
title: Problemas com JDBC/ODBC & quadro Apache Thrift - Azure HDInsight
description: Não é possível descarregar grandes conjuntos de dados usando a estrutura de software JDBC/ODBC e Apache Thrift em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83653596"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Não é possível descarregar grandes conjuntos de dados usando a estrutura de software JDBC/ODBC e Apache Thrift em HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar descarregar grandes conjuntos de dados utilizando jDBC/ODBC e a estrutura de software Apache Thrift em Azure HDInsight, recebe uma mensagem de erro semelhante à seguinte:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Esta exceção é causada pelo processo de serialização que tenta utilizar mais espaço tampão do que é permitido. No Spark 2.0.0, a classe `org.apache.spark.serializer.KryoSerializer` é usada para serializar objetos quando os dados são acedidos através da estrutura de software Apache Thrift. Uma classe diferente é usada para dados que serão enviados pela rede ou em cache em formato serializado.

## <a name="resolution"></a>Resolução

Aumente o `Kryoserializer` valor do tampão. Adicione uma chave com o nome `spark.kryoserializer.buffer.max` e coloque-a `2047` em spark2 config sob `Custom spark2-thrift-sparkconf` . Reinicie todos os componentes afetados.

> [!IMPORTANT]
> O valor `spark.kryoserializer.buffer.max` deve ser inferior a 2048. Os valores fracionados não são suportados.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
