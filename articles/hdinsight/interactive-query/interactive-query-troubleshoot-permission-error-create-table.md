---
title: Permissão negado erro com tabela de Colmeia Apache em Azure HDInsight
description: Permissão negado erro ao tentar criar uma tabela de Colmeia Apache em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895149"
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

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
