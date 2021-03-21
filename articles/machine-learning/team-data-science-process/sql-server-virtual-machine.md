---
title: Explore dados numa máquina virtual do SQL Server - Processo de Ciência de Dados de Equipa
description: Explore + processar dados e gerar funcionalidades usando Python ou SQL numa máquina virtual SQL Server em Azure.
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
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314630"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Processar Dados Numa Máquina Virtual do SQL Server no Azure
Este documento cobre como explorar dados e gerar funcionalidades para dados armazenados num SQL Server VM em Azure. Este objetivo pode ser completado através de problemas de dados utilizando SQL ou utilizando uma linguagem de programação como python.

> [!NOTE]
> As declarações sql da amostra neste documento assumem que os dados estão no SQL Server. Se não for, consulte o mapa do processo de ciência de dados em nuvem para aprender a mover os seus dados para o SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Utilização de SQL
Descrevemos as seguintes tarefas de conflito de dados nesta secção utilizando o SQL:

1. [Exploração de Dados](#sql-dataexploration)
2. [Geração de Recursos](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Exploração de Dados
Aqui estão algumas amostras de scripts SQL que podem ser usados para explorar lojas de dados no SQL Server.

> [!NOTE]
> Para um exemplo prático, pode utilizar o [conjunto de dados do Nyc Taxi](https://www.andresmh.com/nyctaxitrips/) e consultar o IPNB intitulado NYC Data [wrangling usando IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) para um walk-through de ponta a ponta.
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
Nesta secção, descrevemos formas de gerar funcionalidades utilizando SQL:  

1. [Geração de Recursos Baseados em Contagem](#sql-countfeature)
2. [Geração de Recursos de Binário](#sql-binningfeature)
3. [Rolando as características de uma única coluna](#sql-featurerollout)

> [!NOTE]
> Uma vez que gere funcionalidades adicionais, pode adicioná-las como colunas à mesa existente ou criar uma nova tabela com as funcionalidades adicionais e a chave primária, que pode ser juntada à tabela original. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Geração de Recursos Baseados em Contagem
Os exemplos a seguir demonstram duas formas de gerar características de contagem. O primeiro método utiliza a soma condicional e o segundo método utiliza a cláusula "onde". Estes resultados podem então ser associados à tabela original (utilizando colunas-chave primárias) para ter características de contagem ao lado dos dados originais.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Geração de Recursos de Binário
O exemplo a seguir mostra como gerar funcionalidades encadadas ao ligar (utilizando cinco caixas) uma coluna numérica que pode ser usada como recurso:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```

### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Rolando as características de uma única coluna
Nesta secção, demonstramos como lançar uma única coluna numa tabela para gerar funcionalidades adicionais. O exemplo pressupõe que existe uma coluna de latitude ou longitude na tabela a partir da qual está a tentar gerar funcionalidades.

Aqui está um breve primer sobre dados de localização de latitude/longitude (com recursos do fluxo de stackover [Como medir a precisão da latitude e longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude) Esta orientação é útil para entender antes de incluir a localização como uma ou mais funcionalidades:

* O sinal diz-nos se estamos a norte ou a sul, a leste ou a oeste do globo.
* Uma centena de dígitos não zero diz-nos que estamos a usar longitude, não latitude!
* O dígito de dez dá uma posição a cerca de 1.000 km. Dá-nos informações úteis sobre em que continente ou oceano estamos.
* O dígito das unidades (um grau decimal) dá uma posição até 111 km (60 milhas náuticas, cerca de 69 milhas). Pode dizer-lhe aproximadamente em que estado, país ou região está.
* A primeira casa decimal vale até 11,1 km: pode distinguir a posição de uma grande cidade de uma grande cidade vizinha.
* A segunda casa decimal vale até 1,1 km: pode separar uma aldeia da outra.
* A terceira casa decimal vale até 110 m: pode identificar um grande campo agrícola ou campus institucional.
* A quarta casa decimal vale até 11 m: pode identificar uma parcela de terreno. É comparável à precisão típica de uma unidade GPS não corrigida sem interferências.
* A quinta casa decimal vale até 1,1 m: distingue as árvores umas das outras. A precisão a este nível com as unidades de GPS comerciais só pode ser alcançada com correção diferencial.
* A sexta casa decimal vale até 0,11 m: pode usá-lo para colocar estruturas em detalhe, para projetar paisagens, construir estradas. Deve ser mais do que bom para rastrear os movimentos dos glaciares e dos rios. Isto pode ser conseguido tomando medidas meticulosas com GPS, tais como GPS corrigido diferencialmente.

A informação de localização pode ser aparda da seguinte forma, separando a região, a localização e a informação da cidade. Também pode chamar um ponto final REST, como bing Maps API disponível em [Find a Location by Point](/bingmaps/rest-services/locations/find-a-location-by-point) para obter informações da região/distrito.

```sql
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
```

Estas funcionalidades baseadas na localização podem ser ainda mais utilizadas para gerar funcionalidades de contagem adicionais, tal como descrito anteriormente. 

> [!TIP]
> Pode inserir os registos programáticamente utilizando o seu idioma de eleição. Pode ser necessário inserir os dados em pedaços para melhorar a eficiência da escrita (para um exemplo de como fazê-lo usando pyodbc, consulte [uma amostra da HelloWorld para aceder ao SQLServer com python).](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python) Outra alternativa é inserir dados na base de dados utilizando o [utilitário BCP.](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ligação à aprendizagem automática Azure
A funcionalidade recém-gerada pode ser adicionada como uma coluna a uma mesa existente ou armazenada numa nova tabela e juntada à tabela original para machine learning. As funcionalidades podem ser geradas ou acedidas se já criadas, utilizando o módulo [de Dados de Importação][import-data] em Azure Machine Learning, como mostrado abaixo:

![leitores azureml][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Usando uma linguagem de programação como Python
Utilizar python para explorar dados e gerar funcionalidades quando os dados estão no SQL Server é semelhante aos dados de processamento em Azure blob usando Python como documentado nos [dados do Process Azure Blob no seu ambiente de ciência de dados.](data-blob.md) Carregue os dados da base de dados num quadro de dados de pandas para mais processamento. Documentamos o processo de ligação à base de dados e carregamento dos dados no quadro de dados nesta secção.

O seguinte formato de cadeia de ligação pode ser usado para ligar a uma base de dados SQL Server de Python usando pyodbc (substituir o nome de servidor, dbname, nome de utilizador e palavra-passe pelos seus valores específicos):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A [biblioteca pandas](https://pandas.pydata.org/) em Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O código abaixo lê os resultados devolvidos de uma base de dados do SQL Server num quadro de dados do Pandas:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Agora pode trabalhar com o quadro de dados do Pandas conforme abordado no artigo [Processar dados do Process Azure Blob no seu ambiente de ciência de dados.](data-blob.md)

## <a name="azure-data-science-in-action-example"></a>Azure Data Science em Exemplo de Ação
Para um exemplo de walkthrough do Processo de Ciência de Dados Azure utilizando um conjunto de dados público, consulte o Processo de Ciência de [Dados do Azure em Ação](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data