---
title: Explorar os dados em tabelas do Hive com consultas do Hive - Team Data Science Process
description: Utilize scripts de Hive de exemplo que são utilizadas para explorar dados em tabelas do Hive num cluster do HDInsight Hadoop.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c9761ade493641b6445cf151424f9598ab25e384
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880102"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive

Este artigo fornece os scripts de Hive de exemplo que são utilizadas para explorar dados em tabelas do Hive num cluster do HDInsight Hadoop.

Esta tarefa é um passo na [Team Data Science Process](overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [criar uma conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md)
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [personalizar o Azure HDInsight Hadoop Clusters do Advanced Analytics](customize-hadoop-cluster.md).
* Os dados tem sido carregados para tabelas do Hive em clusters do Hadoop de HDInsight do Azure. Se não tiver, siga as instruções em [criar e carregar dados para tabelas do Hive](move-hive-tables.md) para carregar dados para tabelas do Hive em primeiro lugar.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [aceder a cabeça nó de Cluster do Hadoop](customize-hadoop-cluster.md).
* Se precisar de instruções sobre como submeter consultas do Hive, veja [como submeter consultas do Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts de consulta do Hive de exemplo para exploração de dados
1. Obter a contagem de observações por partição  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obter a contagem de observações por dia  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obter os níveis numa coluna categórico  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obter o número de níveis na combinação de duas colunas categóricas  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obter a distribuição para colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrair os registos de associar as duas tabelas
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicionais para cenários de dados de viagens de táxis
Exemplos de consultas que são específicas [dados de viagens de táxis NYC](https://chriswhong.com/open-data/foil_nyc_taxi/) cenários também são fornecidos na [repositório do GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para serem submetidas para executar.

