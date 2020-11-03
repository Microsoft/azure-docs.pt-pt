---
title: Permissão negado erro com tabela de Colmeia Apache em Azure HDInsight
description: Permissão negado erro ao tentar criar uma tabela de Colmeia Apache em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288893"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Cenário: Permissão negada erro ao tentar criar uma tabela de Colmeia Apache em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Verá o seguinte erro ao tentar criar uma tabela:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Verá uma mensagem de erro semelhante se executar o seguinte comando de armazenamento HDFS:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Causa

A capacidade de criar uma tabela na Colmeia Apache é decidida pelas permissões aplicadas à conta de armazenamento do cluster. Se as permissões da conta de armazenamento do cluster estiverem incorretas, não poderá criar tabelas. Isto significa que você pode ter as políticas ranger corretas para a criação de mesa, e ainda ver erros "Permissões Negadas".

## <a name="resolution"></a>Resolução

Isto é causado pela falta de permissões suficientes no recipiente de armazenamento que está a ser utilizado. O utilizador que cria a tabela Hive precisa de ler, escrever e executar permissões contra o recipiente. Para mais informações, consulte [as melhores práticas para a autorização da colmeia utilizando o Apache Ranger em HDP 2.2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]