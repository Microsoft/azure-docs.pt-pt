---
title: Explore dados numa máquina virtual do SQL Server - Team Data Science Process
description: Explore + processe dados e gere funcionalidades usando Python ou SQL numa máquina virtual Do Servidor SQL no Azure.
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
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718485"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Processar Dados Numa Máquina Virtual do SQL Server no Azure
Este documento cobre como explorar dados e gerar funcionalidades para dados armazenados num VM de servidor SQL no Azure. Este objetivo pode ser completado através de discussões de dados utilizando o SQL ou utilizando uma linguagem de programação como a Python.

> [!NOTE]
> As declarações sQL da amostra neste documento assumem que os dados estão no Servidor SQL. Se não for, consulte o mapa do processo de ciência de dados em nuvem para aprender a mover os seus dados para o SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Usando SQL
Descrevemos as seguintes tarefas de luta de dados nesta secção utilizando o SQL:

1. [Exploração de Dados](#sql-dataexploration)
2. [Geração de Recursos](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Exploração de Dados
Aqui estão algumas amostras de scripts SQL que podem ser usados para explorar lojas de dados no SQL Server.

> [!NOTE]
> Para um exemplo prático, pode utilizar o conjunto de dados do [Táxi NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando o IPython Notebook e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

1. Obtenha a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obtenha os níveis numa coluna categórica
   
    `select  distinct <column_name> from <databasename>`
3. Obtenha o número de níveis em combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obtenha a distribuição para colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Geração de Recursos
Nesta secção, descrevemos formas de gerar funcionalidades usando O SQL:  

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Geração de recursos de fixação](#sql-binningfeature)
3. [Lançando as características de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Uma vez gerando funcionalidades adicionais, pode adicioná-las como colunas à tabela existente ou criar uma nova tabela com as características adicionais e a chave primária, que pode ser unida com a tabela original. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Os exemplos seguintes demonstram duas formas de gerar características de contagem. O primeiro método utiliza a soma condicional e o segundo método utiliza a cláusula "onde". Estes resultados podem então ser unidos com a tabela original (utilizando colunas de chaves primárias) para ter características de contagem ao lado dos dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Geração de recursos de fixação
O exemplo seguinte mostra como gerar características binadas, fixando (utilizando cinco caixas) uma coluna numérica que pode ser usada como recurso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Lançando as características de uma única coluna
Nesta secção, demonstramos como lançar uma única coluna numa tabela para gerar características adicionais. O exemplo pressupõe que há uma coluna de latitude ou longitude na tabela a partir da qual está a tentar gerar características.

Aqui está um breve primer sobre dados de localização latitude/longitude (recursos de empilhamento [Como medir a precisão da latitude e longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude) Esta orientação é útil de compreender antes de incluir a localização como uma ou mais funcionalidades:

* O sinal diz-nos se estamos a norte ou a sul, a leste ou a oeste do globo.
* Um não zero centenas de dígitos diz-nos que estamos a usar longitude, não latitude!
* Os 10 dígitos dão uma posição a cerca de 1.000 km. Dá-nos informações úteis sobre em que continente ou oceano estamos.
* O dígito das unidades (um grau decimal) dá uma posição até 111 km (60 milhas náuticas, cerca de 69 milhas). Pode dizer-lhe aproximadamente em que estado, país ou região está.
* O primeiro lugar decimal vale até 11,1 km: pode distinguir a posição de uma grande cidade de uma grande cidade vizinha.
* O segundo lugar decimal vale até 1,1 km: pode separar uma aldeia da próxima.
* O terceiro lugar decimal vale até 110 m: pode identificar um grande campo agrícola ou campus institucional.
* O quarto lugar decimal vale até 11 m: pode identificar uma parcela de terreno. É comparável à precisão típica de uma unidade gps não corrigida sem interferência.
* O quinto lugar decimal vale até 1,1 m: distingue árvores umas das outras. A precisão a este nível com unidades de GPS comerciais só pode ser alcançada com correção diferencial.
* O sexto lugar decimal vale até 0,11 m: você pode usá-lo para definir estruturas em detalhe, para projetar paisagens, construindo estradas. Deve ser mais do que bom o suficiente para rastrear movimentos de glaciares e rios. Isto pode ser conseguido tomando medidas meticulosas com GPS, tais como GPS corrigido diferenciado.

As informações de localização podem ser afunidas da seguinte forma, separando informações de região, localização e cidade. Você também pode chamar um ponto final REST, como Bing Maps API disponível em [Find a Location by Point](https://msdn.microsoft.com/library/ff701710.aspx) para obter a informação região/distrito.

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

Estas funcionalidades baseadas na localização podem ser mais utilizadas para gerar características de contagem adicionais, como descrito anteriormente. 

> [!TIP]
> Pode inserir os registos programáticamente utilizando a sua linguagem de eleição. Pode ser necessário inserir os dados em pedaços para melhorar a eficiência da escrita (por exemplo, como fazê-lo usando pyodbc, consulte [uma amostra HelloWorld para aceder ao SQLServer com pitão](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Outra alternativa é inserir dados na base de dados utilizando o [utilitário BCP.](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ligação à Aprendizagem automática azure
A funcionalidade recém-gerada pode ser adicionada como coluna a uma mesa existente ou armazenada numa nova tabela e unida com a tabela original para machine learning. As funcionalidades podem ser geradas ou acedidas se já forem criadas, utilizando o módulo [de Dados de Importação][import-data] em Aprendizagem Automática Azure, como mostrado abaixo:

![leitores em azul][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Usando uma linguagem de programação como Python
Usar python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados em Blob Azure usando Python como documentado em dados do [Process Azure Blob no seu ambiente](data-blob.md)de ciência de dados. Carregue os dados da base de dados num quadro de dados pandas para mais processamento. Documentamos o processo de ligação à base de dados e carregamos os dados no quadro de dados desta secção.

O seguinte formato de cadeia de ligação pode ser usado para ligar a uma base de dados do SQL Server de Python utilizando pyodbc (substitua o nome do servidor, o nome de utilizador e a palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O código abaixo lê os resultados devolvidos de uma base de dados do SQL Server para um quadro de dados pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora pode trabalhar com o quadro de dados pandas conforme coberto no artigo [Process a Azure Blob dados no seu ambiente](data-blob.md)de ciência de dados .

## <a name="azure-data-science-in-action-example"></a>Azure Data Science in Action Example
Para um exemplo de passagem de ponta a ponta do Processo de Ciência de Dados do Azure utilizando um conjunto de dados público, consulte o Processo de Ciência de [Dados do Azure em Ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

