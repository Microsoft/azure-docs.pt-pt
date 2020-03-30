---
title: Compreenda a Apache Spark para os desenvolvedores u-SQL do Lago de Dados Azure.
description: Este artigo descreve conceitos Apache Spark para ajudá-lo a diferenças entre desenvolvedores U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648431"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Compreender o Apache Spark para programadores de U-SQL

A Microsoft suporta vários serviços de Analytics, como [o Azure Databricks](../azure-databricks/what-is-azure-databricks.md) e [o Azure HDInsight,](../hdinsight/hdinsight-overview.md) bem como o Azure Data Lake Analytics. Ouvimos dos desenvolvedores que têm uma clara preferência por soluções de código aberto à medida que constroem gasodutos de análise. Para ajudar os desenvolvedores da U-SQL a entender o Apache Spark, e como podes transformar os teus scripts U-SQL em Apache Spark, criámos esta orientação.

Inclui uma série de passos que pode tomar, e várias alternativas.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Passos para transformar U-SQL em Apache Spark

1. Transforme os seus oleodutos de orquestração de emprego.

   Se utilizar a [Azure Data Factory](../data-factory/introduction.md) para orquestrar os seus scripts Azure Data Lake Analytics, terá de os ajustar para orquestrar os novos programas Spark.
2. Compreenda as diferenças entre como u-SQL e Spark gerem dados

   Se pretender transferir os seus dados do [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [o Azure Data Lake Storage Gen2,](../storage/blobs/data-lake-storage-introduction.md)terá de copiar tanto os dados dos ficheiros como os dados mantidos pelo catálogo. Note que o Azure Data Lake Analytics apenas suporta o Azure Data Lake Storage Gen1. Ver [Compreender formatos](understand-spark-data-formats.md) de dados da Spark
3. Transforme os seus scripts U-SQL em Spark

   Antes de transformar os seus scripts U-SQL, terá de escolher um serviço de análise. Alguns dos serviços de cálculo disponíveis são:
      - Dados da fábrica de [dados azure dataFlow](../data-factory/concepts-data-flow-overview.md) Os fluxos de dados de mapeamento são transformações de dados projetadas visualmente que permitem aos engenheiros de dados desenvolver uma lógica de transformação de dados gráficos sem escrever código. Embora não sejam adequados para executar código susponsado, podem facilmente representar transformações tradicionais de fluxo de dados semelhantes a SQL
      - [Colmeia Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) A Apache Hive no HDInsight é adequada para operações de extração, transformação e carga (ETL). Isto significa que vais traduzir os teus scripts U-SQL para a Colmeia Apache.
      - Motores De faísca Apache tais como [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) ou [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) Isto significa que vai traduzir os seus scripts U-SQL para Spark. Para mais informações, consulte [compreender os formatos de dados da Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Tanto os [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) como o [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) são serviços de cluster e não empregos sem servidor, como o Azure Data Lake Analytics. Terá de considerar como fornecer os clusters para obter a relação custo/desempenho adequada e como gerir a sua vida útil para minimizar os seus custos. Estes serviços têm características de desempenho diferentes com código de utilizador escrito em .NET, pelo que terá de escrever invólucros ou reescrever o seu código num idioma suportado. Para mais informações, consulte [compreender os formatos de dados da Spark](understand-spark-data-formats.md), Compreenda os conceitos de código Apache Spark para [desenvolvedores U-SQL](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Passos seguintes

- [Compreender os formatos de dados da Spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [Compreender conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize as suas soluções de análise de big data do Azure Data Lake Storage Gen1 para O Armazenamento de Lagos Azure Data Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transforme dados utilizando a atividade da Urticária Hadoop na Fábrica de Dados Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [Transforme dados utilizando a atividade da Spark na Fábrica de Dados Azure](../data-factory/transform-data-using-spark.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
