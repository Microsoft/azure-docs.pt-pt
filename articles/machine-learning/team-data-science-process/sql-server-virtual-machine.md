---
title: Explorar dados em um SQL Server máquina virtual-processo de ciência de dados da equipe
description: Explore + processar dados e gerar recursos usando o Python ou o SQL em uma máquina virtual SQL Server no Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 877c639c35378b173b6ec9c8697725e3b3c09290
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053620"
---
# <a name="heading"></a>Processar dados em SQL Server máquina virtual no Azure
Este documento aborda como explorar dados e gerar recursos para dados armazenados em uma VM SQL Server no Azure. Isso pode ser feito por data Wrangling usando o SQL ou usando uma linguagem de programação como Python.

> [!NOTE]
> As instruções SQL de exemplo neste documento pressupõem que os dados estão em SQL Server. Se não estiver, consulte o mapa do processo de ciência de dados de nuvem para saber como mover seus dados para SQL Server.
> 
> 

## <a name="SQL"></a>Usando o SQL
Descrevemos as seguintes tarefas de Wrangling de dados nesta seção usando SQL:

1. [Exploração de dados](#sql-dataexploration)
2. [Geração de recursos](#sql-featuregen)

### <a name="sql-dataexploration"></a>Exploração de dados
Aqui estão alguns scripts SQL de exemplo que podem ser usados para explorar armazenamentos de dados em SQL Server.

> [!NOTE]
> Para obter um exemplo prático, você pode usar o conjunto de dados de [táxi NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado [NYC data Wrangling usando o Notebook ipython e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para obter um passo a passo de ponta a ponta.
> 
> 

1. Obter a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis em uma coluna categórica
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis em combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição para colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Geração de recursos
Nesta seção, descrevemos maneiras de gerar recursos usando o SQL:  

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Geração de recursos compartimentalização](#sql-binningfeature)
3. [Distribuir os recursos de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que podem ser unidas com a tabela original. 
> 
> 

### <a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Os exemplos a seguir demonstram duas maneiras de gerar recursos de contagem. O primeiro método usa Sum condicional e o segundo método usa a cláusula ' Where '. Em seguida, eles podem ser Unidos com a tabela original (usando colunas de chave primária) para ter recursos de contagem ao lado dos dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Geração de recursos compartimentalização
O exemplo a seguir mostra como gerar recursos de compartimentalizados por compartimentalização (usando cinco compartimentos) uma coluna numérica que pode ser usada como um recurso, em vez disso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Distribuir os recursos de uma única coluna
Nesta seção, demonstraremos como distribuir uma única coluna em uma tabela para gerar recursos adicionais. O exemplo supõe que haja uma coluna de latitude ou longitude na tabela da qual você está tentando gerar recursos.

Aqui está uma breve linha de dados de localização de latitude/longitude (reoriginado de StackOverflow [como medir a precisão da latitude e longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Isso é útil para entender antes de destacar o campo de localização:

* O sinal nos informa se somos norte ou Sul, leste ou oeste do globo.
* Um dígito de centenas diferente de zero indica que estamos usando a longitude, e não latitude!
* O dígito de dezenas dá uma posição de cerca de 1.000 quilômetros. Ele nos fornece informações úteis sobre o continente ou o oceano em que estamos.
* O dígito de unidades (um grau decimal) fornece uma posição de até 111 quilômetros (60 milhas náuticas, cerca de 69 milhas). Ele pode lhe dizer aproximadamente que estado, país ou região em que você está.
* A primeira casa decimal vale até 11,1 km: ela pode distinguir a posição de uma grande cidade de uma grande cidade aproximada.
* A segunda casa decimal vale até 1,1 km: ela pode separar um vila do próximo.
* A terceira casa decimal vale até 110 m: ela pode identificar um grande campo agricultural ou um campus institucional.
* A quarta casa decimal vale até 11 m: ela pode identificar um terreno. Ele é comparável à precisão típica de uma unidade GPS não corrigida sem interferência.
* A quinta casa decimal vale até 1,1 m: ela distingue as árvores umas das outras. A precisão desse nível com unidades de GPS comercial só pode ser obtida com correção diferencial.
* A sexta casa decimal vale até 0,11 m: você pode usá-la para dispor estruturas em detalhes, para criar cenários, criando estradas. Ele deve ser mais do que bom o suficiente para acompanhar os movimentos de geleiras e rios. Isso pode ser feito por meio de medidas criteriosas com GPS, como o GPS com uma diferença diferencial.

As informações de local podem ser destacadosdas da seguinte maneira, separando a região, a localização e as informações de cidade. Observe que você também pode chamar um ponto de extremidade REST, como a API do Bing Maps, disponível em [localizar um local por ponto](https://msdn.microsoft.com/library/ff701710.aspx) para obter as informações de região/distrito.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Esses recursos baseados em local podem ser usados mais para gerar recursos de contagem adicionais, conforme descrito anteriormente. 

> [!TIP]
> Você pode inserir os registros programaticamente usando o idioma de sua escolha. Talvez seja necessário inserir os dados em partes para melhorar a eficiência de gravação (para obter um exemplo de como fazer isso usando pyodbc, consulte [uma amostra HelloWorld para acessar o SqlServer com Python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Outra alternativa é inserir dados no banco de dado usando o [utilitário bcp](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Conectando-se ao Azure Machine Learning
O recurso recém-gerado pode ser adicionado como uma coluna a uma tabela existente ou armazenado em uma nova tabela e Unido à tabela original para o aprendizado de máquina. Os recursos podem ser gerados ou acessados se já tiverem sido criados, usando o módulo [importar dados][import-data] no Azure Machine Learning, conforme mostrado abaixo:

![leitores do azureml][1] 

## <a name="python"></a>Usando uma linguagem de programação como Python
Usar o Python para explorar dados e gerar recursos quando os dados estão em SQL Server é semelhante a processar dados no blob do Azure usando o Python, conforme documentado em [processar dados de blob do Azure em seu ambiente de ciência de dados](data-blob.md). Os dados precisam ser carregados a partir do banco de dados em um data frame do pandas e, em seguida, podem ser processados. Documentamos o processo de conexão com o banco de dados e o carregamento dos dados no quadro data nesta seção.

O seguinte formato de cadeia de conexão pode ser usado para se conectar a um banco de dados SQL Server do Python usando pyodbc (substitua ServerName, dbname, username e password por seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca pandas](https://pandas.pydata.org/) no Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para a manipulação de dados para a programação em Python. O código a seguir lê os resultados retornados de um banco de dados SQL Server em um quadro de dados pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora você pode trabalhar com o quadro de dados pandas, conforme abordado no artigo [processar dados de blob do Azure em seu ambiente de ciência de dados](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Exemplo de ciência de dados do Azure em ação
Para obter um exemplo detalhado de ponta a ponta do processo de ciência de dados do Azure usando um conjunto de dados público, consulte [processo do Azure data Science em ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

