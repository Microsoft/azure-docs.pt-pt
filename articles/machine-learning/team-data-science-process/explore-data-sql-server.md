---
title: Explorar os dados na máquina virtual do SQL Server - Team Data Science Process
description: Como explorar os dados são armazenados numa VM do SQL Server no Azure através de uma linguagem de programação, como o Python ou SQL.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720100"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados numa máquina Virtual do SQL Server no Azure

Este artigo aborda como explorar os dados são armazenados numa VM do SQL Server no Azure. Utilize a SQL ou a Python para examinar os dados.

Esta tarefa é um passo no Processo de Ciência de Dados da [Equipa.](overview.md)

> [!NOTE]
> As instruções de SQL de exemplo neste documento partem do princípio de que os dados estão no SQL Server. Se não for, veja o mapa do processo de ciência dados na cloud para aprender a mover seus dados para o SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Explore os dados SQL com scripts SQL
Aqui estão alguns scripts de SQL de exemplo que podem ser utilizadas para explorar os arquivos de dados no SQL Server.

1. Obter a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis numa coluna categórico
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis na combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição para colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Para um exemplo prático, pode utilizar o conjunto de dados do [Táxi NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando o IPython Notebook e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

## <a name="python"></a>Explore os dados SQL com Python
Usar python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados em Blob Azure usando Python, como documentado em dados do [Process Azure Blob no seu ambiente](data-blob.md)de ciência de dados. Carregue os dados da base de dados num DataFrame pandas e, em seguida, pode ser processado mais. Iremos documentar o processo de ligar à base de dados e carregar os dados para o pacote de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server a partir de Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O seguinte código lê os resultados retornados de uma base de dados do SQL Server num quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora pode trabalhar com o Pandas DataFrame conforme abordado no tópico [Processo De dados De Blob no seu ambiente](data-blob.md)de ciência de dados .

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Para um exemplo de passagem de ponta a ponta do Processo de Análise cortana utilizando um conjunto de dados público, consulte o Processo de Ciência de Dados da Equipa em ação: utilizando o [Servidor SQL](sql-walkthrough.md).

