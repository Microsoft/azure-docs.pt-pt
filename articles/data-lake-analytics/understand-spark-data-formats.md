---
title: Compreenda os formatos de dados da Apache Spark para os desenvolvedores U-SQL do Azure Data Lake Analytics.
description: Este artigo descreve conceitos de Apache Spark para ajudar os desenvolvedores U_SQL a compreender as diferenças entre os formatos de dados U-SQL e Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221116"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Compreender diferenças entre formatos de dados U-SQL e Spark

Se pretender utilizar a [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) ou [a Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)recomendamos que migrar os seus dados de [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Além de mover os seus ficheiros, também vai querer tornar os seus dados, armazenados em tabelas U-SQL, acessíveis ao Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Mover dados armazenados em ficheiros Azure Data Lake Storage Gen1

Os dados armazenados em ficheiros podem ser movidos de várias formas:

- Escreva um pipeline [da Azure Data Factory](../data-factory/introduction.md) para copiar os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para a conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Escreva um trabalho de Spark que leia os dados da conta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) e escreva-os na conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Com base no seu caso de utilização, poderá querer escrevê-lo num formato diferente, como o Parquet, se não precisar de preservar o formato original do ficheiro.

Recomendamos que reveja o artigo [Atualize as suas soluções de análise de big data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Mover dados armazenados em tabelas U-SQL

As tabelas U-SQL não são compreendidas pela Spark. Se tiver dados armazenados em tabelas U-SQL, executará um trabalho U-SQL que extrai os dados da tabela e os guarda num formato que a Spark compreende. O formato mais adequado é criar um conjunto de ficheiros Parquet seguindo o layout da pasta da Metástaa Hive.

A saída pode ser alcançada em U-SQL com o outputter Parquet incorporado e utilizando a partição dinâmica de saída com conjuntos de ficheiros para criar as pastas de partição. [Processar mais ficheiros do que nunca e utilizar o Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) fornece um exemplo de como criar tais dados consumíveis spark.

Após esta transformação, copia os dados descritos no capítulo [Move os dados armazenados nos ficheiros Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Ressalvas

- Semântica de dados Ao copiar ficheiros, a cópia ocorrerá ao nível do byte. Assim, os mesmos dados devem aparecer na conta [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Note no entanto, Spark pode interpretar alguns caracteres de forma diferente. Por exemplo, pode utilizar um padrão diferente para um delimiter de linha num ficheiro CSV.
    Além disso, se estiver a copiar dados dactilografadas (a partir de tabelas), então Parquet e Spark podem ter uma precisão e escala diferentes para alguns dos valores dactilografado (por exemplo, uma boia) e podem tratar valores nulos de forma diferente. Por exemplo, o U-SQL tem a semântica C# para valores nulos, enquanto a Spark tem uma lógica de três valores para valores nulos.

- As tabelas U-SQL da organização de dados (partição) fornecem partição de dois níveis. O nível exterior `PARTITIONED BY` () é por valor e mapas principalmente no esquema de partição colmeia/faísca usando hierarquias de pasta. Terá de se certificar de que os valores nulos estão mapeados para a pasta certa. O nível interno `DISTRIBUTED BY` em U-SQL oferece 4 esquemas de distribuição: robin redondo, alcance, haxixe e haxixe direto.
    As tabelas Hive/Spark suportam apenas a partição de valor ou partição de haxixe, utilizando uma função de haxixe diferente da U-SQL. Quando desativar os dados da tabela U-SQL, provavelmente só será capaz de mapear a partição de valor para a Spark e poderá ter de fazer uma afinação adicional do seu layout de dados dependendo das suas consultas finais de Spark.

## <a name="next-steps"></a>Passos seguintes

- [Compreenda os conceitos de código Spark para desenvolvedores U-SQL](understand-spark-code-concepts.md)
- [Atualize as suas soluções de análise de big data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET para Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade da Spark na Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade da Colmeia Hadoop na Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)