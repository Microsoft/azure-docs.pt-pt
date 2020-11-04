---
title: Explore dados em tabelas de Colmeia com consultas de Colmeia - Processo de Ciência de Dados de Equipa
description: Use scripts de hive de amostra que são usados para explorar dados em tabelas de Colmeia em um cluster HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321123"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive

Este artigo fornece scripts de hive de amostra que são usados para explorar dados em tabelas de Hive em um cluster HDInsight Hadoop.

Esta tarefa é um passo no [Processo de Ciência de Dados](overview.md)da Equipa.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Aderiu um cluster Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Os Clusters Hadoop Azure HDInsight para Análise Avançada](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Os dados foram enviados para as tabelas hive em clusters Azure HDInsight Hadoop. Caso contrário, siga as instruções em [Criar e carregue os dados nas tabelas da Hive](move-hive-tables.md) para enviar primeiro os dados para as tabelas da Colmeia.
* Habilitado acesso remoto ao cluster. Se precisar de instruções, consulte [o nó de cabeça do Conjunto Hadoop](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Se precisar de instruções sobre como submeter consultas de Colmeia, consulte [Como Submeter Consultas de Colmeia](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exemplo Scripts de consulta de hive para exploração de dados
1. Obtenha a contagem de observações por partição  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obtenha a contagem de observações por dia  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obtenha os níveis numa coluna categórica  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obtenha o número de níveis em combinação de duas colunas categóricas  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obtenha a distribuição para colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrair registos de junção de duas mesas
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados de viagem de táxi
Exemplos de consultas específicas aos cenários [de datas de viagem de táxi de NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório gitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) Estas consultas já têm esquema de dados especificado e estão prontas para serem submetidas a execução.