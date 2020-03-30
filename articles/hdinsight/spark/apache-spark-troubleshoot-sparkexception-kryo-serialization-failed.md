---
title: Problemas com jDBC/ODBC & apache Thrift quadro - Azure HDInsight
description: Incapaz de descarregar grandes conjuntos de dados usando a estrutura de software JDBC/ODBC e Apache Thrift em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894265"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Incapaz de descarregar grandes conjuntos de dados usando a estrutura de software JDBC/ODBC e Apache Thrift no HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar descarregar grandes conjuntos de dados utilizando jDBC/ODBC e a estrutura de software Apache Thrift no Azure HDInsight, recebe uma mensagem de erro semelhante à seguinte:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Esta exceção é causada pelo processo de serialização tentando usar mais espaço tampão do que é permitido. Em Spark 2.0.0, `org.apache.spark.serializer.KryoSerializer` a classe é usada para serializar objetos quando os dados são acedidos através da estrutura de software Apache Thrift. Uma classe diferente é usada para dados que serão enviados sobre a rede ou em cache em formade série.

## <a name="resolution"></a>Resolução

Aumente `Kryoserializer` o valor do tampão. Adicione uma `spark.kryoserializer.buffer.max` chave nomeada `2048` e coloque-a `Custom spark2-thrift-sparkconf`em spark2 config abaixo .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
