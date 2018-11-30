---
title: Explorar os dados na máquina Virtual do SQL Server no Azure | Documentos da Microsoft
description: Como explorar os dados são armazenados numa VM do SQL Server no Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: da01a1a386e1ffc590f4cbbb85d9d50a180d4973
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442935"
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
> Para obter um exemplo prático, pode utilizar o [conjunto de dados de táxis de NYC](http://www.andresmh.com/nyctaxitrips/) e consulte IPNB intitulada [preparação de dados de NYC usando SQL Server e o IPython Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para uma passo a passo-a-ponto.
> 
> 

## <a name="python"></a>Explorar os dados SQL com Python
Com o Python para explorar dados e gerar recursos quando os dados estão no SQL Server é semelhante ao processamento de dados em BLOBs do Azure com o Python, conforme documentado [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md). Os dados tem de ser carregados a partir da base de dados num pandas DataFrame e, em seguida, podem ser processados ainda mais. Iremos documentar o processo de ligar à base de dados e carregar os dados para o pacote de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server a partir de Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

O [biblioteca de Pandas](http://pandas.pydata.org/) Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação de Python. O seguinte código lê os resultados retornados de uma base de dados do SQL Server num quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Agora, pode trabalhar com o Pandas DataFrame como abrangido no tópico [dados de Blobs do Azure de processo no seu ambiente de ciência de dados](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Por exemplo passo a passo-a-ponto do Cortana Analytics Process usando um conjunto de dados público, veja [o processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).

