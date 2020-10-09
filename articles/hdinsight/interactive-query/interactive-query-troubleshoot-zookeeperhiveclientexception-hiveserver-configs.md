---
title: Erro do intérprete Apache Hive Zeppelin - Azure HDInsight
description: O Intérprete Da Colmeia Apache Zeppelin JDBC está apontando para o URL errado em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895069"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Cenário: Intérprete de Hive Zeppelin Apache dá um erro de Zookeeper em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Num cluster Apache Hive LLAP, o intérprete Zeppelin dá a seguinte mensagem de erro ao tentar executar uma consulta:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Causa

O Intérprete Zeppelin Hive JDBC está a apontar para a URL errada.

## <a name="resolution"></a>Resolução

1. Navegue para o resumo do componente da Colmeia e copie o "Url Hive JDBC" para a área de transferência.

1. Navegue para `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Editar as definições JDBC: atualizar o valor hive.url para o URL Hive JDBC copiado no passo 1

1. Salve, em seguida, re-tentar a consulta

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
