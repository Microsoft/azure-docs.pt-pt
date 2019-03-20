---
title: Explorar os dados na máquina virtual do SQL Server - Team Data Science Process
description: Como explorar os dados são armazenados numa VM do SQL Server no Azure através de uma linguagem de programação, como o Python ou SQL.
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
ms.openlocfilehash: be75490e4e86956337ce38133df6095790b3a374
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896301"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorar dados numa máquina Virtual do SQL Server no Azure

Este artigo aborda como explorar os dados são armazenados numa VM do SQL Server no Azure. Isso pode ser feito de preparação de dados com o SQL ou com uma linguagem de programação, como o Python.

Esta tarefa é um passo na [Team Data Science Process](overview.md).

> [!NOTE]
> As instruções de SQL de exemplo neste documento partem do princípio de que os dados estão no SQL Server. Se não for, veja o mapa do processo de ciência dados na cloud para aprender a mover seus dados para o SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Explorar os dados SQL com scripts SQL
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
> Para obter um exemplo prático, pode utilizar o [conjunto de dados de táxis de NYC](https://www.andresmh.com/nyctaxitrips/) e consulte IPNB intitulada [preparação de dados de NYC usando SQL Server e o IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma passo a passo-a-ponto.
> 
> 

## <a name="python"></a>Explorar os dados SQL com Python
Com o Python para explorar dados e gerar recursos quando os dados estão no SQL Server é semelhante ao processamento de dados em BLOBs do Azure com o Python, conforme documentado [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md). Os dados tem de ser carregados a partir da base de dados num pandas DataFrame e, em seguida, podem ser processados ainda mais. Iremos documentar o processo de ligar à base de dados e carregar os dados para o pacote de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server a partir de Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [biblioteca de Pandas](https://pandas.pydata.org/) Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O seguinte código lê os resultados retornados de uma base de dados do SQL Server num quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora, pode trabalhar com o Pandas DataFrame como abrangido no tópico [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Por exemplo passo a passo-a-ponto do Cortana Analytics Process usando um conjunto de dados público, veja [o processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).

