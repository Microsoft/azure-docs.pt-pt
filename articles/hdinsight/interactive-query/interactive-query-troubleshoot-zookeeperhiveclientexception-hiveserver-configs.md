---
title: Apache Hive intérprete Zeppelin fornece um erro Zookeeper no Azure HDInsight
description: O intérprete JDBC do Zeppelin hive do Apache está apontando para a URL incorreta no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 185d8e4954091d9b54e0604fc2bd3f3d010f926e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091328"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Cenário: Apache Hive intérprete Zeppelin fornece um erro Zookeeper no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Em um cluster Apache Hive LLAP, o intérprete Zeppelin fornece a seguinte mensagem de erro ao tentar executar uma consulta:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Causa

O intérprete JDBC do hive do Zeppelin está apontando para a URL errada.

## <a name="resolution"></a>Resolução

1. Navegue até o resumo do componente do hive e copie o "URL JDBC do hive" para a área de transferência.

1. Navegue até`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Edite as configurações JDBC: Atualize o valor Hive. URL para a URL JDBC do hive copiada na etapa 1

1. Salve e repita a consulta

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
