---
title: Migrar Apache Spark 2.1 ou 2.2 cargas de trabalho para 2.3 ou 2.4 - Azure HDInsight
description: Saiba como migrar o Apache Spark 2.1 e 2.2 a 2.3 ou 2.4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944748"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migrar Apache Spark 2.1 e 2.2 cargas de trabalho para 2.3 e 2.4

Este documento explica como migrar cargas de trabalho apache spark na Spark 2.1 e 2.2 a 2.3 ou 2.4.

Conforme discutido nas [Notas de Lançamento](../hdinsight-release-notes.md#upcoming-changes), a partir de 1 de julho de 2020, as seguintes configurações do cluster não serão suportadas e os clientes não serão capazes de criar novos clusters com estas configurações:
 - Faísca 2.1 e 2.2 num cluster HDInsight 3.6 Faísca
 - Faísca 2.3 num cluster HDInsight 4.0 Faísca

Os clusters existentes nestas configurações serão executados como-está sem suporte da Microsoft. Se estiver no Spark 2.1 ou 2.2 no HDInsight 3.6, mude para Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar possíveis interrupções do sistema/suporte. Se estiver no Spark 2.3 num cluster HDInsight 4.0, mude para Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar uma possível interrupção do sistema/suporte.

Para obter informações gerais sobre a migração de um cluster HDInsight de 3.6 a 4.0, consulte [o cluster Migração HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md). Para obter informações gerais sobre migração para uma versão mais recente do Apache Spark, consulte [Apache Spark: Version Policy](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Orientações sobre atualizações da versão Spark no HDInsight

| Cenário de atualização | Mecanismo | Coisas a considerar | Integração de faíscas/colmeias |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3.6 Faísca 2.1 a HDInsight 3.6 Faísca 2.3| Recriar clusters com HDInsight Spark 2.3 | Rever os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Upgrade de Spark SQL 2.1 a 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Sem alteração |
|HDInsight 3.6 Faísca 2.2 a HDInsight 3.6 Faísca 2.3 | Recriar clusters com HDInsight Spark 2.3 | Rever os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Sem alteração |
| HDInsight 3.6 Faísca 2.1 a HDInsight 4.0 Faísca 2.4 | Recriar clusters com HDInsight 4.0 Faísca 2.4 | Rever os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Upgrade de Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Upgrade de Spark SQL 2.1 a 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | A integração de faíscas e colmeias mudou em HDInsight 4.0. <br><br> No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder às tabelas SparkSQL ou Hive. Uma mesa criada por Spark vive no catálogo Spark. Uma mesa criada pela Hive vive no catálogo da Hive. Este comportamento é diferente do HDInsight 3.6 onde a Hive e a Spark partilhavam o catálogo comum. A integração de colmeias e faíscas em HDInsight 4.0 depende do Conector do Armazém da Colmeia (HWC). A HWC funciona como uma ponte entre a Spark e a Hive. Saiba mais sobre o Conector do Armazém da Colmeia. <br> Em HDInsight 4.0 se quiser Partilhar a meta-loja entre a Hive e a Spark, pode fazê-lo alterando a propriedade metastore.catalog.default para colmeia no seu cluster Spark. Você pode encontrar esta propriedade em Ambari Advanced spark2-hive-site-override. É importante entender que a partilha de metastões só funciona para mesas de colmeia externas, isto não funcionará se tiver mesas de colmeias internas/geridas ou mesas ACID. <br><br>Leia [Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para obter mais informações.<br><br> |
| HDInsight 3.6 Faísca 2.2 a HDInsight 4.0 Faísca 2.4 | Recriar clusters com HDInsight 4.0 Faísca 2.4 | Rever os seguintes artigos: <br> [Apache Spark: Upgrade de Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Upgrade de Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | A integração de faíscas e colmeias mudou em HDInsight 4.0. <br><br> No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder às tabelas SparkSQL ou Hive. Uma mesa criada por Spark vive no catálogo Spark. Uma mesa criada pela Hive vive no catálogo da Hive. Este comportamento é diferente do HDInsight 3.6 onde a Hive e a Spark partilhavam o catálogo comum. A integração de colmeias e faíscas em HDInsight 4.0 depende do Conector do Armazém da Colmeia (HWC). A HWC funciona como uma ponte entre a Spark e a Hive. Saiba mais sobre o Conector do Armazém da Colmeia. <br> Em HDInsight 4.0 se quiser Partilhar a meta-loja entre a Hive e a Spark, pode fazê-lo alterando a propriedade metastore.catalog.default para colmeia no seu cluster Spark. Você pode encontrar esta propriedade em Ambari Advanced spark2-hive-site-override. É importante entender que a partilha de metastões só funciona para mesas de colmeia externas, isto não funcionará se tiver mesas de colmeias internas/geridas ou mesas ACID. <br><br>Leia [Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para obter mais informações.|

## <a name="next-steps"></a>Passos seguintes

* [Migrar o cluster HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md)
* [Migrar Azure HDInsight 3.6 Cargas de trabalho de colmeia para HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)
