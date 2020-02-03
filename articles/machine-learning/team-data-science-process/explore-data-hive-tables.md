---
title: Explorar os dados em tabelas do Hive com consultas do Hive - Team Data Science Process
description: Utilize scripts de Hive de exemplo que são utilizadas para explorar dados em tabelas do Hive num cluster do HDInsight Hadoop.
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
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722174"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive

Este artigo fornece os scripts de Hive de exemplo que são utilizadas para explorar dados em tabelas do Hive num cluster do HDInsight Hadoop.

Esta tarefa é um passo no Processo de Ciência de Dados da [Equipa.](overview.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem:

* Criar uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Aprovisionar um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Customize Azure HDInsight Hadoop Clusters for Advanced Analytics](customize-hadoop-cluster.md).
* Os dados tem sido carregados para tabelas do Hive em clusters do Hadoop de HDInsight do Azure. Se não o tiver, siga as instruções em [Criar e carregue os dados para as tabelas da Colmeia](move-hive-tables.md) para fazer o upload dos dados para as tabelas da Colmeia primeiro.
* Ativar o acesso remoto para o cluster. Se precisar de instruções, consulte [Aceda ao nó de cabeça do aglomerado de hadoop](customize-hadoop-cluster.md).
* Se precisar de instruções sobre como submeter consultas de Colmeia, consulte [Como Submeter Consultas](move-hive-tables.md#submit) de Colmeia

## <a name="example-hive-query-scripts-for-data-exploration"></a>Scripts de consulta do Hive de exemplo para exploração de dados
1. Obtenha a contagem de observações por divisória `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obtenha a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obter os níveis numa coluna categórico  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obtenha o número de níveis em combinação de duas colunas categóricas `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
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
Exemplos de consultas específicas para os cenários de dados da [viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de táxi de NYC também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já tem o esquema de dados especificado e estão prontas para serem submetidas para executar.

