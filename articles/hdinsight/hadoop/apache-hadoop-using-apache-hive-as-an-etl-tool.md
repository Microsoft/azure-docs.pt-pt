---
title: Utilizar o Apache Hive como uma ferramenta ETL - Azure HDInsight
description: Utilizar o Apache Hive para extrair, transformar e carregar (ETL) de dados no Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: e21f3eed7e631c974d34f148b85843b055deaf60
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123970"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Utilizar o Apache Hive como uma ferramenta de extração, transformação e carregamento (ETL)

Normalmente, terá de limpar e transformar os dados de entrada antes de carregar para um destino adequado para análise. Operações de extração, transformação e carregamento (ETL) são utilizadas para preparar dados e carregá-los para um destino de dados.  Apache Hive no HDInsight pode ler dados não estruturados, processar os dados conforme necessário e, em seguida, carregue os dados para um armazém de dados relacional para sistemas de suporte da decisão. Nesta abordagem, os dados são extraídos da origem e armazenados no armazenamento dimensionável, como blobs de armazenamento do Azure ou o armazenamento do Azure Data Lake. Os dados, em seguida, são transformados com uma seqüência de consultas do Hive e, finalmente, são armazenados dentro do Hive em preparação para em massa ao carregar para o arquivo de dados de destino.

## <a name="use-case-and-model-overview"></a>Utilizar a descrição geral de maiúsculas e modelo

A figura seguinte mostra uma descrição geral do caso de utilização e o modelo para a automatização de ETL. Dados de entrada são transformados para gerar a saída adequada.  Durante essa transformação, os dados podem alterar de forma, o tipo de dados e até mesmo idioma.  Processos de ETL podem converter Imperial métrica, alterar os fusos horários e melhorar a precisão para alinhar adequadamente com os dados existentes no destino.  Processos de ETL também podem combinar dados novo com os dados existentes para manter relatórios atualizados ou para fornecer mais informações sobre os dados existentes.  Aplicativos como o relatório de ferramentas e serviços, em seguida, podem consumir estes dados no formato desejado.

![Apache Hive as ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop é normalmente utilizado em processos ETL que importar um grande número de ficheiros de texto (como CSVs) ou um menor, mas mudam frequentemente o número de ficheiros de texto, ou ambos.  Hive é uma ótima ferramenta para utilizar para preparar os dados antes de carregar para o destino dos dados.  Hive permite-lhe criar um esquema sobre o CSV e utilizar uma linguagem de tipo SQL para gerar programas MapReduce que interagem com os dados. 

As etapas típicas para utilizar o Hive para executar a ETL são os seguintes:

1. Carregar dados para o armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake.
2. Crie uma base de dados de metadados Store (utilizar a base de dados do Azure SQL) para utilizar o Hive em armazenar os seus esquemas.
3. Criar um cluster do HDInsight e ligue-se o arquivo de dados.
4. Defina o esquema para aplicar no tempo de leitura de dados no arquivo de dados:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transformar os dados e carregá-los para o destino.  Existem várias formas de utilizar o Hive durante a transformação e carregamento:

    * Consultar e preparar dados utilizando o Hive e guarde-o como um CSV no armazenamento de Blobs do Azure ou de armazenamento do Azure Data Lake.  Em seguida, utilize uma ferramenta como o SQL Server Integration Services (SSIS) para adquirir os CSVs e carregar os dados para uma base de dados relacional de destino como o SQL Server.
    * Consulte os dados diretamente a partir do Excel ou do c# com o controlador ODBC do Hive.
    * Uso [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para ler os ficheiros CSV simples preparados e carregá-las para a base de dados relacional de destino.

## <a name="data-sources"></a>Origens de dados

Origens de dados são dados externos, normalmente, que podem ser correspondidos a dados existentes no seu arquivo de dados, por exemplo:

* Dados de mídia social, ficheiros de registo, sensores e aplicações que geram ficheiros de dados.
* Conjuntos de dados obtidos de fornecedores de dados, como estatísticas de meteorologia ou fornecedor números de vendas.
* Dados de transmissão em fluxo capturado, filtrado e processados por meio de uma ferramenta adequada ou estrutura.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Destinos de saída

Pode utilizar o Hive para dados de saída a uma variedade de destinos, incluindo:

* Base de dados relacional, como SQL Server ou base de dados do Azure SQL.
* Um armazém de dados, como o Azure SQL Data Warehouse.
* Excel.
* Armazenamento de tabelas e BLOBs do Azure.
* Aplicações ou serviços que exigem dados para serem processados em formatos específicos, ou como os ficheiros que contêm tipos específicos de estrutura de informações.
* Como um Store de documentos JSON <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Considerações

O modelo ETL é normalmente utilizado quando pretender:

* Carregar dados de transmissão ou grandes volumes de dados estruturados ou semiestruturados de fontes externas para uma base de dados existente ou o sistema de informações.
* Limpar, transformar e validar os dados antes de carregá-lo, talvez usando mais do que uma transformação pass-through do cluster.
* Gere relatórios e visualizações que são atualizadas regularmente.  Por exemplo, se o relatório demorar demasiado tempo a gerar durante o dia, pode agendar o relatório seja executado durante a noite.  Pode utilizar o Azure Scheduler e o PowerShell para executar automaticamente uma consulta do Hive.

Se o destino para os dados não é uma base de dados, pode gerar um ficheiro no formato apropriado dentro da consulta, por exemplo, um CSV. Este ficheiro, em seguida, pode ser importado para o Excel ou Power BI.

Se for necessário executar várias operações nos dados como parte do processo de ETL, considere como geri-los. Se as operações são controladas por um programa externo, em vez como um fluxo de trabalho dentro da solução, precisa decidir se algumas operações podem ser executadas em paralelo e para detetar quando cada tarefa estiver concluída. Usando um mecanismo de fluxo de trabalho, como o Oozie dentro do Hadoop pode ser mais fácil do que tentar orquestrar a uma seqüência de operações com scripts externos ou programas personalizados. Para obter mais informações sobre o Oozie, consulte [orquestração do fluxo de trabalho e tarefa](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Passos Seguintes

* [ETL em escala](apache-hadoop-etl-at-scale.md)
* [Operacionalizar um pipeline de dados](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
