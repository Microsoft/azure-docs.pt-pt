---
title: Dados de exemplo no SQL Server no Azure - processo de ciência de dados de equipa
description: Dados de exemplo armazenados no SQL Server no Azure utilizando a linguagem de programação Python ou SQL, em seguida, movem-o para o Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717656"
---
# <a name="heading"></a>Dados da amostra no Servidor SQL no Azure

Este artigo mostra como os dados armazenados no SQL Server no Azure de exemplo usando a linguagem de programação Python ou SQL. Ele também mostra como mover dados de amostras para o Azure Machine Learning ao salvá-lo para um ficheiro, carregá-lo para um blob do Azure e, em seguida, lê-lo para o Azure Machine Learning Studio.

A amostragem python usa a biblioteca [Pyodbc](https://code.google.com/p/pyodbc/) ODBC para ligar ao SQL Server em Azure e à biblioteca [Pandas](https://pandas.pydata.org/) para fazer a amostragem.

> [!NOTE]
> O código de SQL de exemplo neste documento parte do princípio de que os dados estão num SQL Server no Azure. Caso contrário, consulte o [Mover dados para o SQL Server no](move-sql-server-virtual-machine.md) artigo do Azure para obter instruções sobre como mover os seus dados para o SQL Server no Azure.
> 
> 

**Por que provar os seus dados?**
Se o conjunto de dados que pretende analisar for grande, normalmente, é uma boa idéia para dimensionar os dados para reduzi-lo para um tamanho mais pequeno, mas representativo e mais gerenciável. A amostragem facilita a compreensão de dados, a exploração e a engenharia de recursos. O seu papel no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) é permitir a prototipagem rápida das funções de processamento de dados e dos modelos de machine learning.

Esta tarefa de amostragem é um passo no Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>Usando SQL
Esta secção descreve vários métodos com o SQL para executar a amostragem aleatória simple relativamente aos dados na base de dados. Escolha um método com base no tamanho dos dados e sua distribuição.

Os dois itens seguintes mostram como usar `newid` no SQL Server para efetuar a amostragem. O método que escolher depende do quão aleatório pretende que a amostra seja (pk_id no seguinte código de amostra é assumido como uma chave primária autogerada).

1. Menos rígida amostra aleatória
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Amostra mais aleatória 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample pode ser utilizado para também os dados de amostragem. Esta opção pode ser uma melhor abordagem se o tamanho dos seus dados for grande (assumindo que os dados em páginas diferentes não estão correlacionados) e para que a consulta seja completada num tempo razoável.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Pode explorar e gerar recursos a partir destes dados de amostras armazenando-os numa nova tabela
> 
> 

### <a name="sql-aml"></a>Ligação à Aprendizagem automática azure
Pode utilizar diretamente as consultas de amostra acima no módulo de dados de [importação][import-data] de aprendizagem automática Azure para recolher os dados da mosca e trazê-lo para uma experiência de Aprendizagem automática Azure. Uma imagem de utilização do módulo de leitor para ler os dados amostrados é mostrada aqui:

![leitor sql][1]

## <a name="python"></a>Usando a linguagem de programação Python
Esta secção demonstra o uso da [biblioteca pyodbc](https://code.google.com/p/pyodbc/) para estabelecer uma ligação ODBC a uma base de dados de servidores SQL em Python. A cadeia de ligação à base de dados é a seguinte: (substituir o nome do servidor, o nome de utilizador, o nome de utilizador e a palavra-passe pela sua configuração):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A biblioteca [Pandas](https://pandas.pydata.org/) em Python fornece um conjunto rico de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação python. O seguinte código lê uma amostra de 0,1% dos dados de uma tabela na Base de Dados Azure SQL em dados pandas:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Agora, pode trabalhar com dados amostrados na estrutura de dados Pandas. 

### <a name="python-aml"></a>Ligação à Aprendizagem automática azure
Pode utilizar o seguinte código de exemplo para guardar os dados de objeto de amostragem de baixo para um ficheiro e carregá-lo para um blob do Azure. Os dados na bolha podem ser lidos diretamente numa experiência de aprendizagem automática azure utilizando o módulo [de dados de importação.][import-data] Os passos são os seguintes: 

1. Escrever o quadro de dados de pandas num ficheiro local
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Carregar ficheiro local para BLOBs do Azure
   
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
3. Leia os dados do Azure blob utilizando o módulo de dados de [importação][import-data] de aprendizagem automática Azure, como mostram os seguintes ecrãs:

![blob de leitor][2]

## <a name="the-team-data-science-process-in-action-example"></a>O processo de ciência de dados de equipa no exemplo de ação
Para percorrer um exemplo do Processo de Ciência de Dados da Equipa a utilização de um conjunto de dados público, consulte o Processo de Ciência de Dados da Equipa em Ação: utilizando o [Servidor SQL](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
