---
title: Erro do intérprete Apache Hive Zeppelin - Azure HDInsight
description: O Intérprete Da Colmeia Apache Zeppelin JDBC está apontando para o URL errado em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930744"
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

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]