---
title: Entenda Apache Spark formatos de dados para Azure Data Lake Analytics desenvolvedores U-SQL.
description: Este artigo descreve Apache Spark conceitos para ajudar U_SQL os desenvolvedores a entenderem as diferenças entre os formatos de dados U-SQL e Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648444"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Entender as diferenças entre os formatos de dados U-SQL e Spark

Se você quiser usar [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) ou [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), recomendamos que migre seus dados de [Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Além de mover seus arquivos, você também desejará tornar seus dados armazenados em tabelas do U-SQL, acessíveis para o Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Mover dados armazenados em arquivos Azure Data Lake Storage Gen1

Os dados armazenados em arquivos podem ser movidos de várias maneiras:

- Escreva um pipeline de [Azure data Factory](../data-factory/introduction.md) para copiar os dados da conta [Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) para a conta [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) .
- Grave um trabalho do Spark que leia os dados da conta de [Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) e grave-os na conta de [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . Com base no seu caso de uso, talvez você queira escrevê-lo em um formato diferente, como parquet, se não precisar preservar o formato de arquivo original.

Recomendamos que você revise o artigo [atualizar suas soluções de análise de Big data de Azure data Lake Storage Gen1 para Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Mover dados armazenados em tabelas U-SQL

As tabelas U-SQL não são compreendidas pelo Spark. Se você tiver dados armazenados em tabelas U-SQL, executará um trabalho do U-SQL que extrai os dados da tabela e o salva em um formato que o Spark compreende. O formato mais apropriado é criar um conjunto de arquivos parquet seguindo o layout da pasta metastore do Hive.

A saída pode ser obtida no U-SQL com o parquet de saída interno e usando o particionamento dinâmico de saídas com conjuntos de arquivos para criar as pastas de partição. [Processar mais arquivos do que nunca e usar o parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) fornece um exemplo de como criar esses dados de consumo do Spark.

Após essa transformação, você copia os dados conforme descrito no capítulo [mover dados armazenados em Azure data Lake Storage Gen1 arquivos](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>ADVERTÊNCIAS

- Semântica de dados ao copiar arquivos, a cópia ocorrerá no nível de byte. Portanto, os mesmos dados devem ser exibidos na conta de [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . No entanto, observe que o Spark pode interpretar alguns caracteres de forma diferente. Por exemplo, ele pode usar um padrão diferente para um delimitador de linha em um arquivo CSV.
    Além disso, se você estiver copiando dados tipados (de tabelas), o parquet e o Spark podem ter precisão e escala diferentes para alguns dos valores digitados (por exemplo, um float) e podem tratar valores nulos de forma diferente. Por exemplo, o U-SQL tem C# a semântica para valores nulos, enquanto o Spark tem uma lógica de três valores para valores nulos.

- Organização de dados (particionamento) as tabelas U-SQL fornecem particionamento de dois níveis. O nível externo (`PARTITIONED BY`) é por valor e é mapeado principalmente para o esquema de particionamento do hive/Spark usando hierarquias de pastas. Você precisará garantir que os valores nulos sejam mapeados para a pasta correta. O nível interno (`DISTRIBUTED BY`) no U-SQL oferece 4 esquemas de distribuição: round robin, intervalo, hash e hash direto.
    As tabelas do hive/Spark dão suporte apenas ao particionamento de valor ou ao particionamento de hash, usando uma função de hash diferente da U-SQL. Quando você der saída aos dados da tabela U-SQL, provavelmente será possível mapear apenas o particionamento de valor para o Spark e talvez seja necessário ajustar ainda mais o layout dos dados, dependendo das consultas finais do Spark.

## <a name="next-steps"></a>Passos seguintes

- [Entender os conceitos de código do Spark para desenvolvedores do U-SQL](understand-spark-code-concepts.md)
- [Atualize suas soluções de análise de Big Data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET para Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
