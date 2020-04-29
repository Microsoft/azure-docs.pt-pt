---
title: Criar funcionalidades no Servidor SQL utilizando SQL e Python - Team Data Science Process
description: Gere funcionalidades para dados armazenados num SQL Server VM em Azure utilizando sQL e Python - parte do Processo de Ciência de Dados da Equipa.
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
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721749"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Criar características para dados no SQL Server com SQL e Python
Este documento mostra como gerar funcionalidades para dados armazenados num VM de Servidor SQL no Azure que ajudam os algoritmos a aprender de forma mais eficiente a partir dos dados. Você pode usar SQL ou uma linguagem de programação como Python para realizar esta tarefa. Ambas as abordagens são demonstradas aqui.

Esta tarefa é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Para um exemplo prático, pode consultar o conjunto de dados do [Táxi nyc](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando o IPython Notebook e o SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem:

* Criei uma conta de armazenamento Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento Azure](../../storage/common/storage-account-create.md)
* Guardei os seus dados no Servidor SQL. Caso contrário, consulte a Move dados para uma base de [dados Azure SQL para](move-sql-azure.md) obter instruções sobre como mover os dados para lá.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Geração de recursos com SQL
Nesta secção, descrevemos formas de gerar funcionalidades usando O SQL:  

* [Geração de recursos baseada em contagem](#sql-countfeature)
* [Geração de recursos de fixação](#sql-binningfeature)
* [Lançando as características de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Uma vez gerando funcionalidades adicionais, pode adicioná-las como colunas à tabela existente ou criar uma nova tabela com as características adicionais e a chave primária, que pode ser unida com a tabela original.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Geração de recursos baseados em contagem
Este documento demonstra duas formas de gerar características de contagem. O primeiro método utiliza a soma condicional e o segundo método utiliza a cláusula "onde". Estas novas funcionalidades podem então ser unidas à tabela original (utilizando colunas de chaves primárias) para ter características de contagem ao lado dos dados originais.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Geração de recursos de fixação
O exemplo seguinte mostra como gerar características binadas, fixando (utilizando cinco caixas) uma coluna numérica que pode ser usada como recurso:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Lançando as características de uma única coluna
Nesta secção, demonstramos como lançar uma única coluna numa tabela para gerar características adicionais. O exemplo pressupõe que há uma coluna de latitude ou longitude na tabela a partir da qual está a tentar gerar características.

Aqui está um breve primer sobre dados de localização latitude/longitude (recursos provenientes de empilhamento). `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` Aqui estão algumas coisas úteis para entender sobre os dados de localização antes de criar funcionalidades a partir do campo:

* O sinal indica se estamos a norte ou a sul, a leste ou a oeste do globo.
* Um não zero centenas de dígitos indica longitude, não está a ser usada latitude.
* Os 10 dígitos dão uma posição a cerca de 1.000 km. Dá informações úteis sobre em que continente ou oceano estamos.
* O dígito das unidades (um grau decimal) dá uma posição até 111 km (60 milhas náuticas, cerca de 69 milhas). Indica, grosso modo, em que grande estado ou país/região estamos.
* O primeiro lugar decimal vale até 11,1 km: pode distinguir a posição de uma grande cidade de uma grande cidade vizinha.
* O segundo lugar decimal vale até 1,1 km: pode separar uma aldeia da próxima.
* O terceiro lugar decimal vale até 110 m: pode identificar um grande campo agrícola ou campus institucional.
* O quarto lugar decimal vale até 11 m: pode identificar uma parcela de terreno. É comparável à precisão típica de uma unidade gps não corrigida sem interferência.
* O quinto lugar decimal vale até 1,1 m: distingue árvores umas das outras. A precisão a este nível com unidades de GPS comerciais só pode ser alcançada com correção diferencial.
* O sexto lugar decimal vale até 0,11 m: você pode usar este nível para definir estruturas em detalhe, para projetar paisagens, construindo estradas. Deve ser mais do que bom o suficiente para rastrear movimentos de glaciares e rios. Este objetivo pode ser alcançado tomando medidas meticulosas com GPS, tais como GPS corrigido diferenciado.

As informações de localização podem ser afunidas separando informações de região, localização e cidade. Uma vez também pode chamar um ponto final DE `https://msdn.microsoft.com/library/ff701710.aspx` REPOUSO, como bing Maps API (ver para obter a informação região/distrito).

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
> Pode inserir os registos programáticamente utilizando a sua linguagem de eleição. Pode ser necessário inserir os dados em pedaços para melhorar a eficiência da escrita. [Aqui está um exemplo de como fazê-lo usando pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Outra alternativa é inserir dados na base de dados usando [utilitário BCP](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ligação à Aprendizagem automática azure
A funcionalidade recém-gerada pode ser adicionada como coluna a uma mesa existente ou armazenada numa nova tabela e unida com a tabela original para machine learning. As funcionalidades podem ser geradas ou acedidas se já forem criadas, utilizando o módulo [de Dados de Importação](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) em Azure ML, como mostrado abaixo:

![Leitores do Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Usando uma linguagem de programação como Python
Usar python para gerar funcionalidades quando os dados estão no SQL Server é semelhante ao processamento de dados em Blob Azure usando Python. Para comparação, consulte os dados do [Process Azure Blob no seu ambiente](data-blob.md)de ciência de dados. Carregue os dados da base de dados num quadro de dados pandas para processá-lo ainda mais. O processo de ligação à base de dados e de carregamento dos dados no quadro de dados está documentado nesta secção.

O seguinte formato de cadeia de ligação pode ser usado para ligar a uma base de dados do SQL Server de Python utilizando pyodbc (substitua o nome do servidor, o nome de utilizador e a palavra-passe com os seus valores específicos):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [biblioteca Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O seguinte código lê os resultados devolvidos de uma base de dados do SQL Server para um quadro de dados do Pandas:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Agora pode trabalhar com o quadro de dados pandas como abordado em tópicos Criar funcionalidades para dados de [armazenamento de blob Azure usando panda](create-features-blob.md).

