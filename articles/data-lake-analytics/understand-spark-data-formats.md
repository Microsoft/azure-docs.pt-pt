---
title: Compreenda os formatos de dados da Apache Spark para os desenvolvedores u-SQL do Lago de Dados Do Azure.
description: Este artigo descreve conceitos Apache Spark para ajudar os desenvolvedores U_SQL a compreender as diferenças entre os formatos de dados U-SQL e Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648444"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Compreender diferenças entre os formatos de dados U-SQL e Spark

Se quiser utilizar os [Tijolos de Dados Azure](../azure-databricks/what-is-azure-databricks.md) ou [o Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)recomendamos que emigra os seus dados do [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Além de mover os seus ficheiros, também vai querer tornar os seus dados armazenados em tabelas U-SQL, acessíveis à Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Mova os dados armazenados em ficheiros Da Gen1 de Armazenamento de Lagos De dados Do Mato

Os dados armazenados em ficheiros podem ser movidos de várias formas:

- Escreva um oleoduto [Azure Data Factory](../data-factory/introduction.md) para copiar os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para a conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Escreva um trabalho de Spark que leia os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) e os escreva na conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Com base no seu caso de utilização, pode querer escrevê-lo num formato diferente, como o Parquet, se não precisar de preservar o formato de ficheiro original.

Recomendamos que reveja o artigo [Atualize as suas soluções de análise de big data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Mova os dados armazenados em tabelas U-SQL

As mesas U-SQL não são compreendidas pela Spark. Se tiver dados armazenados em tabelas U-SQL, executará um trabalho U-SQL que extrai os dados da tabela e os guarde num formato que a Spark entenda. O formato mais adequado é criar um conjunto de ficheiros Parquet seguindo o layout da pasta da metastore da Hive.

A saída pode ser alcançada em U-SQL com o outputter Parquet incorporado e utilizando a divisão dinâmica de saída com conjuntos de ficheiros para criar as pastas de partição. [Processar mais ficheiros do que nunca e usar](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) o Parquet fornece um exemplo de como criar tais dados consumíveis spark.

Após esta transformação, copia os dados conforme descrito no capítulo [Move dados armazenados em ficheiros Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Ressalvas

- Semântica de dados Ao copiar ficheiros, a cópia ocorrerá ao nível do byte. Assim, os mesmos dados devem aparecer na conta De armazenamento de lagos Azure Data [Gen2.](../storage/blobs/data-lake-storage-introduction.md) Note-se, no entanto, Spark pode interpretar alguns caracteres de forma diferente. Por exemplo, pode utilizar um padrão diferente para um delimitador de linha num ficheiro CSV.
    Além disso, se estiver a copiar dados dactilografados (a partir de tabelas), então o Parquet e a Spark podem ter uma precisão e escala diferentes para alguns dos valores digitados (por exemplo, um flutuador) e podem tratar valores nulos de forma diferente. Por exemplo, a U-SQL tem a semântica C# para valores nulos, enquanto a Spark tem uma lógica de três valores valores.

- A organização de dados (partição) as tabelas U-SQL fornecem dois níveis de divisão. O nível`PARTITIONED BY`exterior ( ) é por valor e mapas principalmente no esquema de partição Hive/Spark usando hierarquias de pastas. Terá de garantir que os valores nulos estão mapeados na pasta certa. O nível`DISTRIBUTED BY`interno () em U-SQL oferece 4 esquemas de distribuição: robin redondo, gama, hash e hash direto.
    As tabelas Hive/Spark apenas suportam a divisão de valor ou a divisão de haxixe, utilizando uma função de haxixe diferente da U-SQL. Ao obter os seus dados de tabela U-SQL, provavelmente só será capaz de mapear a divisão de valor para a Spark e poderá ter de fazer uma nova sintonização do seu layout de dados dependendo das suas consultas finais de Spark.

## <a name="next-steps"></a>Passos seguintes

- [Compreender conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize as suas soluções de análise de big data do Azure Data Lake Storage Gen1 para O Armazenamento de Lagos Azure Data Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transforme dados utilizando a atividade da Spark na Fábrica de Dados Azure](../data-factory/transform-data-using-spark.md)
- [Transforme dados utilizando a atividade da Urticária Hadoop na Fábrica de Dados Azure](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
