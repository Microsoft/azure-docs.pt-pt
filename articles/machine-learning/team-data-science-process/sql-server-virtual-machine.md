---
title: Explorar os dados numa máquina virtual do SQL Server - Team Data Science Process
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718485"
---
# <a name="heading"></a>Processar dados na máquina virtual do servidor SQL no Azure
Este documento aborda como explorar dados e gerar características para dados armazenados numa VM do SQL Server no Azure. Este objetivo pode ser completado através de discussões de dados utilizando o SQL ou utilizando uma linguagem de programação como a Python.

> [!NOTE]
> As instruções de SQL de exemplo neste documento partem do princípio de que os dados estão no SQL Server. Se não for, veja o mapa do processo de ciência dados na cloud para aprender a mover seus dados para o SQL Server.
> 
> 

## <a name="SQL"></a>Usando SQL
Descrevemos as seguintes tarefas wrangling dados nesta secção com o SQL:

1. [Exploração de Dados](#sql-dataexploration)
2. [Geração de Recursos](#sql-featuregen)

### <a name="sql-dataexploration"></a>Exploração de Dados
Aqui estão alguns scripts de SQL de exemplo que podem ser utilizadas para explorar os arquivos de dados no SQL Server.

> [!NOTE]
> Para um exemplo prático, pode utilizar o conjunto de dados do [Táxi NYC](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando o IPython Notebook e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

1. Obter a contagem de observações por dia
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obter os níveis numa coluna categórico
   
    `select  distinct <column_name> from <databasename>`
3. Obter o número de níveis na combinação de duas colunas categóricas 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obter a distribuição para colunas numéricas
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Geração de Recursos
Nesta seção, descrevemos as formas de gerar recursos com o SQL:  

1. [Geração de recursos baseada em contagem](#sql-countfeature)
2. [Geração de recursos de fixação](#sql-binningfeature)
3. [Lançando as características de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Depois de gerar recursos adicionais, pode adicioná-los como colunas na tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser associado com a tabela original. 
> 
> 

### <a name="sql-countfeature"></a>Geração de recursos baseada em contagem
Os exemplos seguintes demonstram as duas formas de gerar recursos de contagem. O primeiro método usa soma condicional e o segundo método utiliza a cláusula "where". Estes resultados podem então ser unidos com a tabela original (utilizando colunas de chaves primárias) para ter características de contagem ao lado dos dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Geração de recursos de fixação
O exemplo seguinte mostra como gerar recursos compartimentados por compartimentação (usando cinco discretizações), uma coluna numérica que pode ser utilizada como um recurso em vez disso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Lançando as características de uma única coluna
Nesta secção, vamos demonstrar como implementar uma única coluna numa tabela para gerar recursos adicionais. O exemplo parte do princípio de que existe uma coluna de latitude ou longitude na tabela a partir do qual está a tentar gerar recursos.

Aqui está um breve primer sobre dados de localização latitude/longitude (recursos de empilhamento [Como medir a precisão da latitude e longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude) Esta orientação é útil de compreender antes de incluir a localização como uma ou mais funcionalidades:

* O início de sessão nos informa se estamos Norte ou sul, Leste ou oeste em todo o mundo.
* Um centenas de diferentes de zero dígitos nos informam que estamos a utilizar longitude, latitude não!
* As dezenas dígitos dá uma posição para cerca de 1.000 quilômetros. Ele nos dá informações úteis sobre o continente ou oceano estamos.
* O dígito unidades (decimal um certo grau) fornece uma posição 111 quilômetros (60 milhas náuticas, cerca de 69 quilómetros). Pode dizer-lhe aproximadamente em que estado, país ou região está.
* A primeira casa decimal vale até 11.1 km: ele pode distinguir a posição de uma cidade grandes de uma cidade grandes vizinho.
* A segunda casa decimal vale até 1.1 km: ele pode separar um village da seguinte.
* A terceiro casa decimal, vale a pena m: até 110, poderá identificar um campo de agricultural grandes ou o campus institucional.
* A quarta casa decimal, vale a pena m: até 11, poderá identificar um parcel da terra. É comparável da precisão típica de uma unidade GPS uncorrected com nenhuma interferência.
* A quinta casa decimal, vale a pena m: até 1.1, que distingue árvores uns dos outros. Precisão para este nível com as unidades GPS comerciais só pode ser realizada com a correção diferencial.
* A sexta casa decimal, vale a pena m: até 0.11, que pode utilizá-lo para disposição dos estruturas em detalhes, para a criação de cenários, a criação de estradas. Deve ser mais de bom o suficiente para movimentos de glaciers e rios de controlo. Isso pode ser conseguido ao tirar o trabalho árduo medidas com GPS, como GPS differentially corrigido.

As informações de localização podem ser caracterizadas como a seguir, separar região, a localização e informações de cidade. Você também pode chamar um ponto final REST, como Bing Maps API disponível em [Find a Location by Point](https://msdn.microsoft.com/library/ff701710.aspx) para obter a informação região/distrito.

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

Estas funcionalidades com base na localização podem servir-se ainda mais para gerar recursos adicionais de contagem, conforme descrito anteriormente. 

> [!TIP]
> Por meio de programação, pode inserir os registros usando a linguagem de sua escolha. Pode ser necessário inserir os dados em pedaços para melhorar a eficiência da escrita (por exemplo, como fazê-lo usando pyodbc, consulte [uma amostra HelloWorld para aceder ao SQLServer com pitão](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Outra alternativa é inserir dados na base de dados utilizando o [utilitário BCP.](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Ligação à Aprendizagem automática azure
A funcionalidade gerada recentemente pode ser adicionada como uma coluna para uma tabela existente ou armazenada numa nova tabela e associada com a tabela original para o machine learning. As funcionalidades podem ser geradas ou acedidas se já forem criadas, utilizando o módulo [de Dados de Importação][import-data] em Aprendizagem Automática Azure, como mostrado abaixo:

![leitores do azureml][1] 

## <a name="python"></a>Usando uma linguagem de programação como Python
Usar python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados em Blob Azure usando Python como documentado em dados do [Process Azure Blob no seu ambiente](data-blob.md)de ciência de dados. Carregue os dados da base de dados num quadro de dados pandas para mais processamento. Iremos documentar o processo de ligar à base de dados e carregar os dados para o quadro de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser utilizado para ligar a uma base de dados do SQL Server a partir de Python com pyodbc (substitua servername, dbname, nome de utilizador e palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O código abaixo lê os resultados retornados de uma base de dados do SQL Server num quadro de dados Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora pode trabalhar com o quadro de dados pandas conforme coberto no artigo [Process a Azure Blob dados no seu ambiente](data-blob.md)de ciência de dados .

## <a name="azure-data-science-in-action-example"></a>Ciência de dados do Azure no exemplo de ação
Para um exemplo de passagem de ponta a ponta do Processo de Ciência de Dados do Azure utilizando um conjunto de dados público, consulte o Processo de Ciência de [Dados do Azure em Ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

