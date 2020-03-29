---
title: Explore dados na máquina virtual do SQL Server - Team Data Science Process
description: Como explorar dados que são armazenados num SQL Server VM em Azure usando sQL ou uma linguagem de programação como python.
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
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720100"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados numa máquina Virtual do SQL Server no Azure

Este artigo abrange como explorar dados que são armazenados num VM sQL Server no Azure. Utilize a SQL ou a Python para examinar os dados.

Esta tarefa é um passo no Processo de Ciência de Dados da [Equipa.](overview.md)

> [!NOTE]
> As declarações sQL da amostra neste documento assumem que os dados estão no Servidor SQL. Se não for, consulte o mapa do processo de ciência de dados em nuvem para aprender a mover os seus dados para o SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Explore os dados SQL com scripts SQL
Aqui estão algumas amostras de scripts SQL que podem ser usados para explorar lojas de dados no SQL Server.

1. Obtenha a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obtenha os níveis numa coluna categórica
   
    `select  distinct <column_name> from <databasename>`
3. Obtenha o número de níveis em combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obtenha a distribuição para colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Para um exemplo prático, pode utilizar o conjunto de dados do [Táxi NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando o IPython Notebook e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Explore os dados SQL com Python
Usar python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados em Blob Azure usando Python, como documentado em dados do [Process Azure Blob no seu ambiente](data-blob.md)de ciência de dados. Carregue os dados da base de dados num DataFrame pandas e, em seguida, pode ser processado mais. Documentamos o processo de ligação à base de dados e carregamos os dados no DataFrame nesta secção.

O seguinte formato de cadeia de ligação pode ser usado para ligar a uma base de dados do SQL Server de Python utilizando pyodbc (substitua o nome do servidor, o nome de utilizador e a palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O seguinte código lê os resultados devolvidos de uma base de dados do SQL Server para um quadro de dados do Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora pode trabalhar com o Pandas DataFrame conforme abordado no tópico [Processo De dados De Blob no seu ambiente](data-blob.md)de ciência de dados .

## <a name="the-team-data-science-process-in-action-example"></a>O Processo de Ciência de Dados da Equipa em exemplo de ação
Para um exemplo de passagem de ponta a ponta do Processo de Análise cortana utilizando um conjunto de dados público, consulte o Processo de Ciência de Dados da Equipa em ação: utilizando o [Servidor SQL](sql-walkthrough.md).

