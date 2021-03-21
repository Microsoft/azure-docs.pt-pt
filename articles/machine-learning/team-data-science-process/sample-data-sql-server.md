---
title: Dados da amostra no SQL Server on Azure - Processo de Ciência de Dados de Equipa
description: Os dados da amostra armazenados no SQL Server em Azure utilizando o SQL ou a linguagem de programação Python, em seguida, movê-lo para Azure Machine Learning.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321867"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Dados de exemplo no SQL Server no Azure

Este artigo mostra como recolher dados armazenados no SQL Server em Azure utilizando a linguagem de programação SQL ou Python. Também mostra como mover dados amostrados para Azure Machine Learning guardando-os para um ficheiro, enviando-os para uma bolha Azure e, em seguida, lendo-os no Azure Machine Learning Studio.

A amostragem Python utiliza a biblioteca [Pyodbc](https://code.google.com/p/pyodbc/) ODBC para ligar ao SQL Server em Azure e à biblioteca [pandas](https://pandas.pydata.org/) para fazer a amostragem.

> [!NOTE]
> O código SQL da amostra neste documento pressupõe que os dados estão num Servidor SQL em Azure. Caso contrário, consulte a [Moved datas para SQL Server no artigo Azure](move-sql-server-virtual-machine.md) para obter instruções sobre como mover os seus dados para o SQL Server no Azure.
> 
> 

**Por que provar os seus dados?**
Se o conjunto de dados que pretende analisar é grande, é geralmente uma boa ideia reduzir a amostragem dos dados para reduzi-los a um tamanho menor, mas representativo e mais manejável. A amostragem facilita a compreensão de dados, a exploração e a engenharia de recursos. O seu papel no Processo de Ciência de [Dados de Equipa (TDSP)](./index.yml) é permitir uma rápida prototipagem das funções de processamento de dados e modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de Dados de [Equipa (TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Utilização de SQL
Esta secção descreve vários métodos que utilizam o SQL para efetuar uma simples amostragem aleatória contra os dados na base de dados. Escolha um método baseado no tamanho dos seus dados e na sua distribuição.

Os dois itens seguintes mostram como usar `newid` no SQL Server para realizar a amostragem. O método escolhido depende do quão aleatória deseja que a amostra seja (pk_id no seguinte código de amostra é assumido como uma chave primária autogerada).

1. Amostra aleatória menos rigorosa

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Amostra mais aleatória 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

O conjunto de mesas também pode ser utilizado para a amostragem dos dados. Esta opção pode ser uma melhor abordagem se o seu tamanho de dados for grande (assumindo que os dados em diferentes páginas não estão correlacionados) e para que a consulta seja concluída em um tempo razoável.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Você pode explorar e gerar recursos a partir deste dados amostrados armazenando-os em uma nova tabela
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Ligação à aprendizagem automática Azure
Pode utilizar diretamente as consultas de amostra acima no módulo Azure Machine Learning [Import Data][import-data] para descoduturar os dados da mosca e trazê-lo para uma experiência de Aprendizagem automática Azure. Uma imagem de utilização do módulo de leitor para ler os dados amostrados é mostrada aqui:

![leitor sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Usando a linguagem de programação Python
Esta secção demonstra a utilização da [biblioteca pyodbc](https://code.google.com/p/pyodbc/) para estabelecer uma ligação ODBC a uma base de dados de servidorES SQL em Python. A cadeia de ligação da base de dados é a seguinte: (substitua o nome de servidor, dbname, nome de utilizador e palavra-passe pela sua configuração):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

A biblioteca [pandas](https://pandas.pydata.org/) em Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O seguinte código lê uma amostra de 0,1% dos dados de uma tabela na Base de Dados Azure SQL em dados pandas:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Agora pode trabalhar com os dados recolhidos no quadro de dados do Pandas. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Ligação à aprendizagem automática Azure
Pode utilizar o seguinte código de amostra para guardar os dados de amostras para um ficheiro e enviá-lo para uma bolha Azure. Os dados na bolha podem ser lidos diretamente numa Experiência de Aprendizagem automática Azure utilizando o módulo [de Dados de Importação.][import-data] Os passos são os seguintes: 

1. Escreva o quadro de dados dos pandas para um ficheiro local

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Faça o upload do ficheiro local para a blob Azure

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Leia os dados da bolha Azure utilizando o módulo de [dados de importação de aprendizagem de][import-data] máquina de azure, tal como mostrado na seguinte captura de ecrã:

![bolha leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados da equipa em ação exemplo
Para percorrer um exemplo do Processo de Ciência de Dados de Equipa utilizando um conjunto de dados públicos, consulte [o Processo de Ciência de Dados da Equipa em Ação: utilizando o SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data