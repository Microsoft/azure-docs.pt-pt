---
title: Usando Apache Hive como uma ferramenta ETL – Azure HDInsight
description: Use Apache Hive para extrair, transformar e carregar (ETL) dados no Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: 71631cd2394efd6743bc0e80a458fed2678d4be0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076252"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Usar Apache Hive como uma ferramenta ETL (extração, transformação e carregamento)

Normalmente, você precisa limpar e transformar os dados de entrada antes de carregá-los em um destino adequado para análise. As operações de ETL (extração, transformação e carregamento) são usadas para preparar dados e carregá-los em um destino de dados.  Apache Hive no HDInsight pode ler dados não estruturados, processar os dados conforme necessário e, em seguida, carregar os dados em uma data warehouse relacional para sistemas de suporte a decisões. Nessa abordagem, os dados são extraídos da origem e armazenados em armazenamento escalonável, como BLOBs de armazenamento do Azure ou Azure Data Lake Storage. Os dados são então transformados usando uma sequência de consultas de Hive e, por fim, são preparados no hive em preparação para o carregamento em massa no armazenamento de dados de destino.

## <a name="use-case-and-model-overview"></a>Visão geral de caso de uso e modelo

A figura a seguir mostra uma visão geral do caso de uso e do modelo para a automação de ETL. Os dados de entrada são transformados para gerar a saída apropriada.  Durante essa transformação, os dados podem alterar a forma, o tipo de dados e até mesmo o idioma.  Os processos de ETL podem converter Imperial em métrica, alterar os fusos horários e melhorar a precisão para se alinhar corretamente com os dados existentes no destino.  Os processos de ETL também podem combinar novos dados com os dados existentes para manter o relatório atualizado ou para fornecer mais informações sobre os dados existentes.  Aplicativos como ferramentas de relatório e serviços podem consumir esses dados no formato desejado.

![Apache Hive como arquitetura de ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

O Hadoop é normalmente usado em processos de ETL que importam um grande número de arquivos de texto (como CSVs) ou um número menor, mas frequentemente alterando os arquivos de texto, ou ambos.  O hive é uma ótima ferramenta a ser usada para preparar os dados antes de carregá-los no destino de dados.  O hive permite que você crie um esquema no CSV e use uma linguagem semelhante ao SQL para gerar programas MapReduce que interagem com os dados. 

As etapas típicas para usar o hive para executar ETL são as seguintes:

1. Carregar dados no Azure Data Lake Storage ou no armazenamento de BLOBs do Azure.
2. Crie um banco de dados de repositório de metadados (usando o banco de dados SQL do Azure) para uso pelo Hive no armazenamento de seus esquemas.
3. Crie um cluster HDInsight e conecte o armazenamento de dados.
4. Defina o esquema a ser aplicado em tempo de leitura sobre os dados no armazenamento de dados:

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

5. Transforme os dados e carregue-os no destino.  Há várias maneiras de usar o hive durante a transformação e o carregamento:

    * Consulte e prepare dados usando o hive e salve-os como um CSV no Azure Data Lake Storage ou no armazenamento de BLOBs do Azure.  Em seguida, use uma ferramenta como o SQL Server Integration Services (SSIS) para adquirir esses CSVs e carregar os dados em um banco de dado relacional de destino, como SQL Server.
    * Consulte os dados diretamente do Excel ou C# usando o driver ODBC do hive.
    * Use o [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para ler os arquivos CSV simples preparados e carregá-los no banco de dados relacional de destino.

## <a name="data-sources"></a>Origens de dados

As fontes de dados normalmente são dados externos que podem ser correspondidos aos dados existentes no armazenamento de dados, por exemplo:

* Dados de mídia social, arquivos de log, sensores e aplicativos que geram arquivos de dados.
* Conjuntos de dados obtidos de provedores de data, como estatísticas do clima ou números de vendas do fornecedor.
* Transmissão de dados capturados, filtrados e processados por meio de uma ferramenta ou estrutura adequada.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Destinos de saída

Você pode usar o hive para dados de saída para uma variedade de destinos, incluindo:

* Um banco de dados relacional, como SQL Server ou banco de dados SQL do Azure.
* Um data warehouse, como o SQL Data Warehouse do Azure.
* Excel.
* Armazenamento de tabelas e BLOBs do Azure.
* Aplicativos ou serviços que exigem que os dados sejam processados em formatos específicos ou como arquivos que contêm tipos específicos de estrutura de informações.
* Um repositório de documentos JSON como <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Considerações

O modelo de ETL normalmente é usado quando você deseja:

* Carregue dados de fluxo ou grandes volumes de dados semiestruturados ou não estruturados de fontes externas em um banco de dados ou sistema de informações existente.
* Limpe, transforme e valide os dados antes de carregá-los, talvez usando mais de uma passagem de transformação através do cluster.
* Gere relatórios e visualizações que são atualizadas regularmente.  Por exemplo, se o relatório levar muito tempo para ser gerado durante o dia, você poderá agendar o relatório para ser executado à noite.  Você pode usar o Agendador do Azure e o PowerShell para executar automaticamente uma consulta de Hive.

Se o destino dos dados não for um banco de dado, você poderá gerar um arquivo no formato apropriado dentro da consulta, por exemplo, um CSV. Esse arquivo pode então ser importado para o Excel ou Power BI.

Se você precisar executar várias operações nos dados como parte do processo de ETL, considere como gerenciá-las. Se as operações forem controladas por um programa externo, em vez de um fluxo de trabalho dentro da solução, você precisará decidir se algumas operações podem ser executadas em paralelo e detectar quando cada trabalho é concluído. Usar um mecanismo de fluxo de trabalho como Oozie no Hadoop pode ser mais fácil do que tentar orquestrar uma sequência de operações usando scripts externos ou programas personalizados. Para obter mais informações sobre Oozie, consulte [fluxo de trabalho e orquestração de trabalhos](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Passos Seguintes

* [ETL em escala](apache-hadoop-etl-at-scale.md)
* [Colocar um pipeline de dados em operação](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
