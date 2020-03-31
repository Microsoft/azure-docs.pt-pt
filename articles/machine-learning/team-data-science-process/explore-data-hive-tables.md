---
title: Explore dados em tabelas da Colmeia com consultas de Colmeia - Processo de Ciência de Dados da Equipa
description: Utilize scripts de colmeia de amostra que são usados para explorar dados em tabelas da Colmeia num cluster De Hadoop HDInsight.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722174"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorar dados em tabelas do Hive com consultas do Hive

Este artigo fornece scripts de amostra da Hive que são usados para explorar dados em tabelas da Colmeia em um cluster Hadoop HDInsight.

Esta tarefa é um passo no Processo de Ciência de Dados da [Equipa.](overview.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Forprovisionou um cluster Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Customize Azure HDInsight Hadoop Clusters for Advanced Analytics](customize-hadoop-cluster.md).
* Os dados foram enviados para as tabelas da Hive em clusters Hadoop Azure HDInsight. Se não o tiver, siga as instruções em [Criar e carregue os dados para as tabelas da Colmeia](move-hive-tables.md) para fazer o upload dos dados para as tabelas da Colmeia primeiro.
* Acesso remoto ativado ao cluster. Se precisar de instruções, consulte [Aceda ao nó de cabeça do aglomerado de hadoop](customize-hadoop-cluster.md).
* Se precisar de instruções sobre como submeter consultas de Colmeia, consulte [Como Submeter Consultas](move-hive-tables.md#submit) de Colmeia

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exemplo, scripts de consulta de Hive para exploração de dados
1. Obtenha a contagem de observações por partição`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Obtenha a contagem de observações por dia`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Obtenha os níveis numa coluna categórica  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Obtenha o número de níveis em combinação de duas colunas categóricas`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Obtenha a distribuição para colunas numéricas  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Extrair registos da junção de duas tabelas
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts de consulta adicional para cenários de dados de viagem de táxi
Exemplos de consultas específicas para os cenários de dados da [viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de táxi de NYC também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas já têm dados de esquema saem e estão prontos para serem submetidos a execução.

