---
title: Explore dados em máquina virtual SQL Server - Processo de Ciência de Dados de Equipa
description: Como explorar dados que são armazenados num SQL Server VM em Azure usando SQL ou uma linguagem de programação como Python.
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "86085655"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados numa máquina Virtual do SQL Server no Azure

Este artigo abrange como explorar dados que são armazenados num SQL Server VM em Azure. Utilize SQL ou Python para examinar os dados.

Esta tarefa é um passo no [Processo de Ciência de Dados](overview.md)da Equipa.

> [!NOTE]
> As declarações sql da amostra neste documento assumem que os dados estão no SQL Server. Se não for, consulte o mapa do processo de ciência de dados em nuvem para aprender a mover os seus dados para o SQL Server.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Explore dados SQL com scripts SQL
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
> Para um exemplo prático, pode utilizar o [conjunto de dados do Nyc Taxi](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Explore dados SQL com Python
A utilização do Python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante aos dados de processamento em Azure blob usando Python, como documentado nos [dados do Process Azure Blob no seu ambiente de ciência de dados.](data-blob.md) Carregue os dados da base de dados num DataFrame de pandas e, em seguida, pode ser processado mais adiante. Documentamos o processo de ligação à base de dados e de carregamento dos dados no DataFrame nesta secção.

O seguinte formato de cadeia de ligação pode ser usado para ligar a uma base de dados SQL Server de Python usando pyodbc (substituir o nome de servidor, dbname, nome de utilizador e palavra-passe pelos seus valores específicos):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A [biblioteca pandas](https://pandas.pydata.org/) em Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O código seguinte lê os resultados devolvidos de uma base de dados do SQL Server num quadro de dados do Pandas:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Agora pode trabalhar com o DataFrame pandas como abordado no tópico [Process Azure Blob dados no seu ambiente de ciência de dados.](data-blob.md)

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados da equipa em exemplo de ação
Para obter um exemplo de walkthrough do Processo de Análise cortana utilizando um conjunto de dados públicos, consulte [o Processo de Ciência de Dados da Equipa em ação: utilizando](sql-walkthrough.md)o SQL Server .

