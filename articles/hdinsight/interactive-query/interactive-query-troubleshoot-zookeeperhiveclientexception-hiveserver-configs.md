---
title: Erro do intérprete Apache Hive Zeppelin - Azure HDInsight
description: O Intérprete Da Colmeia Apache Zeppelin JDBC está apontando para o URL errado em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288703"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]