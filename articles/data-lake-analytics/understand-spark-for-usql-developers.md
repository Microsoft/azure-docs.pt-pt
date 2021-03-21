---
title: Compreenda o Apache Spark para os desenvolvedores U-SQL do Azure Data Lake Analytics.
description: Este artigo descreve conceitos de Apache Spark para ajudá-lo a diferenças entre desenvolvedores U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221099"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Compreender o Apache Spark para programadores de U-SQL

A Microsoft suporta vários serviços de Analytics, tais como [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) e [Azure HDInsight,](../hdinsight/hdinsight-overview.md) bem como Azure Data Lake Analytics. Ouvimos dos desenvolvedores que têm uma clara preferência por soluções de código aberto à medida que constroem oleodutos de análise. Para ajudar os desenvolvedores da U-SQL a entender o Apache Spark, e como podes transformar os teus scripts U-SQL em Apache Spark, criámos esta orientação.

Inclui uma série de passos que pode tomar, e várias alternativas.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Passos para transformar u-SQL em Apache Spark

1. Transforme os seus oleodutos de orquestração de trabalho.

   Se utilizar [a Azure Data Factory](../data-factory/introduction.md) para orquestrar os seus scripts Azure Data Lake Analytics, terá de os ajustar para orquestrar os novos programas Spark.
2. Compreenda as diferenças entre a forma como a U-SQL e a Spark gerem dados

   Se pretender transferir os seus dados do [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [a Azure Data Lake Storage Gen2,](../storage/blobs/data-lake-storage-introduction.md)terá de copiar tanto os dados do ficheiro como os dados mantidos pelo catálogo. Note que a Azure Data Lake Analytics apenas suporta a Azure Data Lake Storage Gen1. Ver [Compreender formatos de dados spark](understand-spark-data-formats.md)
3. Transforme os seus scripts U-SQL em Spark

   Antes de transformar os seus scripts U-SQL, terá de escolher um serviço de análise. Alguns dos serviços de computação disponíveis são:
      - [Azure Data Factory DataFlow](../data-factory/concepts-data-flow-overview.md) Mapear fluxos de dados são transformações de dados visualmente projetadas que permitem aos engenheiros de dados desenvolver uma lógica de transformação de dados gráficos sem escrever código. Embora não sejam adequados para executar código de utilizador complexo, eles podem facilmente representar transformações tradicionais de fluxo de dados semelhantes a SQL
      - [Colmeia Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) A Colmeia Apache em HDInsight é adequada para operações de extração, transformação e carga (ETL). Isto significa que vais traduzir os teus scripts U-SQL para a Apache Hive.
      - Motores Apache Spark tais como [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) ou [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) Isto significa que você vai traduzir seus scripts U-SQL para Spark. Para obter mais informações, consulte [os formatos de dados da Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Tanto [a Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) como [a Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) são serviços de cluster e não trabalhos sem servidor como o Azure Data Lake Analytics. Terá de considerar como providenciar os clusters para obter a relação custo/desempenho adequada e como gerir a sua vida útil para minimizar os seus custos. Estes serviços têm características de desempenho diferentes com o código do utilizador escrito em .NET, pelo que terá de escrever invólucros ou reescrever o seu código num idioma suportado. Para obter mais informações, consulte [os formatos de dados da Spark,](understand-spark-data-formats.md) [compreenda os conceitos de código Apache Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Passos seguintes

- [Compreender os formatos de dados da Spark para desenvolvedores U-SQL](understand-spark-data-formats.md)
- [Compreenda os conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize as suas soluções de análise de big data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET para Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade da Colmeia Hadoop na Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformar dados usando a atividade da Spark na Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)