---
title: Usando a Colmeia Apache como uma ferramenta ETL - Azure HDInsight
description: Utilize a Apache Hive para extrair, transformar e carregar dados (ETL) no Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623124"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Use a colmeia Apache como uma ferramenta de extrato, transformação e carga (ETL)

Normalmente é necessário limpar e transformar os dados de entrada antes de os carregar num destino adequado para análise. As operações de extração, transformação e carga (ETL) são utilizadas para preparar dados e carregá-lo num destino de dados.  A Apache Hive no HDInsight pode ler em dados não estruturados, processar os dados conforme necessário e, em seguida, carregar os dados num armazém de dados relacionais para sistemas de suporte de decisão. Nesta abordagem, os dados são extraídos da fonte e armazenados em armazenamento escalável, tais como blobs de armazenamento Azure ou armazenamento de lagos de dados Azure. Os dados são então transformados usando uma sequência de consultas da Colmeia e são finalmente encenados dentro da Colmeia em preparação para o carregamento a granel na loja de dados de destino.

## <a name="use-case-and-model-overview"></a>Use a visão geral do caso e do modelo

A figura que se segue mostra uma visão geral da caixa de utilização e do modelo para a automatização ETL. Os dados de entrada são transformados para gerar a saída adequada.  Durante essa transformação, os dados podem mudar a forma, o tipo de dados e até mesmo a linguagem.  Os processos ETL podem converter o Imperial em métricas, alterar fusos horários e melhorar a precisão para alinhar adequadamente com os dados existentes no destino.  Os processos da ETL também podem combinar novos dados com os dados existentes para continuar a reportar atualizado, ou para fornecer mais informações sobre os dados existentes.  Aplicações como ferramentas e serviços de reporte podem então consumir estes dados no formato pretendido.

![Colmeia Apache como arquitetura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

O hadoop é normalmente usado em processos ETL que importam um número maciço de ficheiros de texto (como CSVs) ou um menor, mas frequentemente mudando o número de ficheiros de texto, ou ambos.  A Hive é uma ótima ferramenta para preparar os dados antes de os carregar no destino de dados.  A Hive permite-lhe criar um esquema sobre o CSV e usar uma linguagem semelhante a SQL para gerar programas MapReduce que interagem com os dados.

Os passos típicos para usar a Colmeia para executar o ETL são os seguintes:

1. Carregue os dados no Armazenamento do Lago Azure Data ou no Armazenamento de Blob Azure.
2. Crie uma base de dados da Loja de Metadados (utilizando a Base de Dados Azure SQL) para utilização pela Hive para armazenar os seus esquemas.
3. Crie um cluster HDInsight e ligue a loja de dados.
4. Defina o esquema a aplicar no tempo de leitura sobre os dados na loja de dados:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Transforme os dados e carregue-os no destino.  Existem várias formas de usar a Colmeia durante a transformação e carregamento:

    * Consulta e prepare dados usando a Hive e guarde-os como CSV no Armazenamento do Lago De Dados Azure ou no armazenamento de blob Azure.  Em seguida, utilize uma ferramenta como o SQL Server Integration Services (SSIS) para adquirir esses CSVs e carregar os dados numa base de dados relacional de destino, como o SQL Server.
    * Consultar os dados diretamente C# do Excel ou utilizando o condutor hive ODBC.
    * Utilize [o Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para ler os ficheiros CSV planos preparados e carregá-los na base de dados relacional do destino.

## <a name="data-sources"></a>Origens de dados

As fontes de dados são normalmente dados externos que podem ser comparados com os dados existentes na sua loja de dados, por exemplo:

* Dados das redes sociais, ficheiros de registo, sensores e aplicações que geram ficheiros de dados.
* Conjuntos de dados obtidos a partir de fornecedores de dados, tais como estatísticas meteorológicas ou números de vendas de fornecedores.
* Dados de streaming capturados, filtrados e processados através de uma ferramenta ou estrutura adequadas.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Metas de saída

Pode utilizar a Hive para obter dados para uma variedade de alvos, incluindo:

* Uma base de dados relacional, como o SQL Server ou a Base de Dados Azure SQL.
* Um armazém de dados, como o Azure SQL Data Warehouse.
* Excel.
* Mesa azul e armazenamento de bolhas.
* Aplicações ou serviços que exigem que os dados sejam processados em formatos específicos, ou como ficheiros que contenham tipos específicos de estrutura de informação.
* Uma Loja de Documentos JSON como [a Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

## <a name="considerations"></a>Considerações

O modelo ETL é normalmente utilizado quando se quer:

* Dados de fluxo de carga ou grandes volumes de dados semi-estruturados ou não estruturados de fontes externas para uma base de dados ou sistema de informação existente.
* Limpe, transforme e valide os dados antes de carregá-lo, talvez usando mais de uma transformação passar pelo cluster.
* Gerar relatórios e visualizações que são regularmente atualizados. Por exemplo, se o relatório demorar demasiado tempo a gerar durante o dia, pode agendar o relatório para ser executado à noite. Para executar automaticamente uma consulta de Hive, pode utilizar [aplicações da Azure Logic](../../logic-apps/logic-apps-overview.md) e PowerShell.

Se o alvo dos dados não for uma base de dados, pode gerar um ficheiro no formato apropriado dentro da consulta, por exemplo, um CSV. Este ficheiro pode então ser importado para Excel ou Power BI.

Se necessitar de executar várias operações nos dados como parte do processo ETL, considere como os gere. Se as operações forem controladas por um programa externo, e não como um fluxo de trabalho dentro da solução, é necessário decidir se algumas operações podem ser executadas paralelamente e detetar quando cada trabalho termina. Usar um mecanismo de fluxo de trabalho como o Oozie dentro de Hadoop pode ser mais fácil do que tentar orquestrar uma sequência de operações usando scripts externos ou programas personalizados. Para obter mais informações sobre Oozie, consulte [Workflow e orquestração de emprego.](https://msdn.microsoft.com/library/dn749829.aspx)

## <a name="next-steps"></a>Passos seguintes

* [ETL em escala](apache-hadoop-etl-at-scale.md)
* [Operacionalizar um gasoduto de dados](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
