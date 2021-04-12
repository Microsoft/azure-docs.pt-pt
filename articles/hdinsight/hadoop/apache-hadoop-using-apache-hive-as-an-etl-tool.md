---
title: Utilização da Colmeia Apache como ferramenta ETL - Azure HDInsight
description: Utilize a Colmeia Apache para extrair, transformar e carregar dados (ETL) em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: bcf2f39423f033ccd5bfdb6bf51ebc89e254f802
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867819"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Use a Colmeia Apache como uma ferramenta de extrato, transformação e carga (ETL)

Normalmente, é necessário limpar e transformar dados de entrada antes de os carregar num destino adequado para análise. As operações de extração, transformação e carga (ETL) são utilizadas para preparar dados e carregá-lo num destino de dados.  A Apache Hive on HDInsight pode ler em dados não estruturados, processar os dados conforme necessário e, em seguida, carregar os dados num armazém de dados relacional para sistemas de suporte de decisão. Nesta abordagem, os dados são extraídos da fonte. Em seguida, armazenado em armazenamento adaptável, como bolhas de armazenamento Azure ou armazenamento do Lago de Dados Azure. Os dados são então transformados usando uma sequência de consultas de Colmeia. Em seguida, encenou dentro da Colmeia em preparação para o carregamento a granel na loja de dados de destino.

## <a name="use-case-and-model-overview"></a>Use a visão geral do caso e do modelo

A seguinte figura mostra uma visão geral da caixa de utilização e do modelo para a automatização ETL. Os dados de entrada são transformados para gerar a saída adequada.  Durante essa transformação, os dados mudam de forma, tipo de dados e até mesmo linguagem.  Os processos ETL podem converter o Imperial em métrica, alterar fusos horários e melhorar a precisão para se alinharem adequadamente com os dados existentes no destino. Os processos ETL também podem combinar novos dados com dados existentes para continuar a reportar atualizado, ou para fornecer informações adicionais sobre os dados existentes. Aplicações como ferramentas e serviços de reporte podem então consumir estes dados no formato procurado.

:::image type="content" source="./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png" alt-text="Colmeia Apache como arquitetura ETL" border="false":::

Hadoop é normalmente usado em processos ETL que importam um número maciço de ficheiros de texto (como CSVs). Ou um número menor, mas frequentemente alterado, de ficheiros de texto, ou ambos.  A Colmeia é uma ótima ferramenta para preparar os dados antes de os colocar no destino dos dados.  A Colmeia permite-lhe criar um esquema sobre o CSV e usar uma linguagem semelhante ao SQL para gerar programas MapReduce que interagem com os dados.

Os passos típicos para usar a Colmeia para fazer ETL são os seguintes:

1. Carregue os dados no Azure Data Lake Storage ou no Azure Blob Storage.
2. Crie uma base de dados da Loja de Metadados (utilizando a Base de Dados Azure SQL) para utilização pela Hive no armazenamento dos seus esquemas.
3. Crie um cluster HDInsight e ligue a loja de dados.
4. Defina o esquema para aplicar no tempo de leitura sobre os dados na loja de dados:

    ```hql
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

    * Consultar e preparar dados usando a Colmeia e guardá-lo como um CSV no Azure Data Lake Storage ou armazenamento de blob Azure.  Em seguida, utilize uma ferramenta como os SQL Server Integration Services (SSIS) para adquirir esses CSVs e carregar os dados numa base de dados relacional de destino, como o SQL Server.
    * Consultar os dados diretamente do Excel ou C# utilizando o controlador Hive ODBC.
    * Utilize [o Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) para ler os ficheiros CSV planos preparados e carregue-os na base de dados relacional do destino.

## <a name="data-sources"></a>Origens de dados

As fontes de dados são normalmente dados externos que podem ser correspondidos aos dados existentes na sua loja de dados, por exemplo:

* Dados das redes sociais, ficheiros de registo, sensores e aplicações que geram ficheiros de dados.
* Conjuntos de dados obtidos a partir de fornecedores de dados, tais como estatísticas meteorológicas ou números de vendas de fornecedores.
* Dados de streaming capturados, filtrados e processados através de uma ferramenta ou estrutura adequada.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Metas de saída

Pode utilizar a Hive para obter dados de produção para diferentes tipos de alvos, incluindo:

* Uma base de dados relacional, como o SQL Server ou a Base de Dados Azure SQL.
* Um armazém de dados, como a Azure Synapse Analytics.
* Excel.
* Mesa azul e armazenamento de bolhas.
* Aplicações ou serviços que exigem que os dados sejam processados em formatos específicos, ou como ficheiros que contenham tipos específicos de estrutura de informação.
* Uma Loja de Documentos JSON como a Azure Cosmos DB.

## <a name="considerations"></a>Considerações

O modelo ETL é normalmente utilizado quando se deseja:

`*` Carregue os dados de fluxo ou grandes volumes de dados semi-estruturados ou não estruturados de fontes externas para uma base de dados ou sistema de informação existente.
`*` Limpe, transforme e valide os dados antes de os carregar, talvez utilizando mais do que uma transformação passe pelo cluster.
`*` Gere relatórios e visualizações que são regularmente atualizados. Por exemplo, se o relatório demorar demasiado tempo a gerar durante o dia, pode agendar o relatório para ser executado à noite. Para executar automaticamente uma consulta de Hive, pode utilizar apps e PowerShell [da Azure Logic.](../../logic-apps/logic-apps-overview.md)

Se o alvo para os dados não for uma base de dados, pode gerar um ficheiro no formato apropriado dentro da consulta, por exemplo um CSV. Este ficheiro pode então ser importado para Excel ou Power BI.

Se precisar de executar várias operações nos dados como parte do processo ETL, considere como os gere. Com operações controladas por um programa externo, e não como um fluxo de trabalho dentro da solução, decida se algumas operações podem ser executadas em paralelo. E detetar quando cada trabalho termina. Usar um mecanismo de fluxo de trabalho como Oozie dentro de Hadoop pode ser mais fácil do que tentar orquestrar uma sequência de operações usando scripts externos ou programas personalizados.

## <a name="next-steps"></a>Passos seguintes

* [ETL em escala](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
