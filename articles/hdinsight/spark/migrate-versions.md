---
title: Emigrar Apache Spark 2.1 ou 2.2 cargas de trabalho para 2.3 ou 2.4 - Azure HDInsight
description: Aprenda a migrar Apache Spark 2.1 e 2.2 a 2.3 ou 2.4.
author: ashishthaps1
ms.author: ashishth
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1452a22303af169e9501c85336785f7627ac7e88
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727628"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migração Apache Spark 2.1 e 2.2 cargas de trabalho para 2.3 e 2.4

Este documento explica como migrar as cargas de trabalho da Apache Spark em Spark 2.1 e 2.2 a 2.3 ou 2.4.

Conforme discutido nas Notas de [Lançamento](../hdinsight-release-notes.md#upcoming-changes), a partir de 1 de julho de 2020, as seguintes configurações de cluster não serão suportadas e os clientes não serão capazes de criar novos clusters com estas configurações:
 - Faísca 2.1 e 2.2 num cluster hDInsight 3.6 Spark
 - Faísca 2.3 em um cluster HDInsight 4.0 Faísca

Os clusters existentes nestas configurações serão executados como está sem suporte da Microsoft. Se estiver na Spark 2.1 ou 2.2 no HDInsight 3.6, mude para Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar uma possível interrupção do sistema/suporte. Se estiver no Spark 2.3 num cluster HDInsight 4.0, mude para Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar uma possível interrupção do sistema/suporte.

Para obter informações gerais sobre a migração de um cluster HDInsight de 3.6 a 4.0, consulte o [cluster Migrate HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md). Para obter informações gerais sobre a migração para uma versão mais recente do Apache Spark, consulte [Apache Spark: Versioning Policy](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Orientação sobre atualizações da versão Spark no HDInsight

| Cenário de atualização | Mecanismo | Coisas a considerar | Integração da Colmeia de Faíscas |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3.6 Faísca 2.1 para HDInsight 3.6 Faísca 2.3| Recriar clusters com HDInsight Spark 2.3 | Reveja os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Upgrade de Spark SQL 2.1 para 2.2](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-21-to-22) | Sem Alteração |
|HDInsight 3.6 Faísca 2.2 para HDInsight 3.6 Faísca 2.3 | Recriar clusters com HDInsight Spark 2.3 | Reveja os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) | Sem Alteração |
| HDInsight 3.6 Faísca 2.1 para HDInsight 4.0 Faísca 2.4 | Recriar clusters com HDInsight 4.0 Spark 2.4 | Reveja os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Upgrade de Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Upgrade de Spark SQL 2.1 para 2.2](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-21-to-22) | A Integração spark Hive mudou em HDInsight 4.0. <br><br> No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder a mesas SparkSQL ou Hive. Uma mesa criada pela Spark vive no catálogo spark. Uma mesa criada pela Hive vive no catálogo da Hive. Este comportamento é diferente do HDInsight 3.6 onde a Hive e a Spark partilharam o catálogo comum. A Integração da Hive e Spark no HDInsight 4.0 depende do Conector de Armazém hive (HWC). A HWC funciona como uma ponte entre A Faísca e a Colmeia. Saiba mais sobre o Conector de Armazém hive. <br> No HDInsight 4.0 se quiser partilhar a metaloja entre a Hive e a Spark, pode fazê-lo alterando a metastore da propriedade.catalog.default para colmeia no seu cluster Spark. Você pode encontrar esta propriedade em Ambari Advanced spark2-hive-site-override. É importante entender que a partilha de metaloja funciona apenas para mesas de colmeia externas, isso não funcionará se tiver mesas de colmeia internas/geridas ou mesas ACID. <br><br>Leia as cargas de trabalho da [Emigração Azure HDInsight 3.6 Hive para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para obter mais informações.<br><br> |
| HDInsight 3.6 Faísca 2.2 para HDInsight 4.0 Faísca 2.4 | Recriar clusters com HDInsight 4.0 Spark 2.4 | Reveja os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Upgrade de Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide-upgrade.html#upgrading-from-spark-sql-22-to-23) | A Integração spark Hive mudou em HDInsight 4.0. <br><br> No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder a mesas SparkSQL ou Hive. Uma mesa criada pela Spark vive no catálogo spark. Uma mesa criada pela Hive vive no catálogo da Hive. Este comportamento é diferente do HDInsight 3.6 onde a Hive e a Spark partilharam o catálogo comum. A Integração da Hive e Spark no HDInsight 4.0 depende do Conector de Armazém hive (HWC). A HWC funciona como uma ponte entre A Faísca e a Colmeia. Saiba mais sobre o Conector de Armazém hive. <br> No HDInsight 4.0 se quiser partilhar a metaloja entre a Hive e a Spark, pode fazê-lo alterando a metastore da propriedade.catalog.default para colmeia no seu cluster Spark. Você pode encontrar esta propriedade em Ambari Advanced spark2-hive-site-override. É importante entender que a partilha de metaloja funciona apenas para mesas de colmeia externas, isso não funcionará se tiver mesas de colmeia internas/geridas ou mesas ACID. <br><br>Leia as cargas de trabalho da [Emigração Azure HDInsight 3.6 Hive para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para obter mais informações.|

## <a name="next-steps"></a>Próximos passos

* [Migrar o cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Migrar azure HDInsight 3.6 Cargas de trabalho da Colmeia para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)
