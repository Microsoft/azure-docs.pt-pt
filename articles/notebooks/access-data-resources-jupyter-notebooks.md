---
title: Dados de acesso em cadernos Jupyter - Pré-visualização de cadernos Azure
description: Saiba como aceder a ficheiros, APIs de REST, bases de dados e diferentes recursos de Armazenamento Azure a partir de um bloco de notas Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: b2254e6d966ca3281cd9c8b0771cb77fb6dede33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846620"
---
# <a name="access-cloud-data-in-a-notebook"></a>Aceder a dados da cloud num bloco de notas

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Fazer um trabalho interessante num caderno jupyter requer dados. Data, na verdade, é a força vital dos cadernos.

Você certamente pode [importar ficheiros de dados em um projeto](work-with-project-data-files.md), mesmo usando comandos como dentro de um `curl` portátil para descarregar um arquivo diretamente. É provável, no entanto, que você precise trabalhar com dados muito mais extensos que estão disponíveis a partir de fontes não-arquivo, tais como APIs REST, bases de dados relacionais e armazenamento em nuvem, como tabelas Azure.

Este artigo descreve brevemente estas diferentes opções. Como o acesso aos dados é melhor visto em ação, pode encontrar código exequível nas Amostras de [Cadernos Azure - Aceda aos seus dados](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>APIs REST

De um modo geral, a grande quantidade de dados disponíveis na Internet é acedida não através de ficheiros, mas através de APIs REST. Felizmente, como uma célula de portátil pode conter qualquer código que você gosta, você pode usar código para enviar pedidos e receber dados JSON. Em seguida, pode converter esse JSON em qualquer formato que queira utilizar, como um dataframe de pandas.

Para aceder a dados utilizando uma API REST, utilize o mesmo código nas células de código de um portátil que utiliza em qualquer outra aplicação. A estrutura geral que utiliza a biblioteca de pedidos é a seguinte:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database e SQL Managed Instance

Pode aceder a bases de dados em SQL Database ou SQL Managed Instance com a ajuda das bibliotecas pyodbc ou pymssql.

[Use python para consultar uma base de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) dá-lhe instruções sobre a criação de uma base de dados na Base de Dados SQL contendo dados adventureWorks, e mostra como consultar esses dados. O mesmo código é indicado no caderno de amostras deste artigo.

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage fornece vários tipos diferentes de armazenamento não relacional, dependendo do tipo de dados que tem e da forma como precisa de aceder a ele:

- Armazenamento de mesa: fornece armazenamento de baixo custo e de alto volume para dados tabulares, tais como registos de sensores recolhidos, registos de diagnóstico, e assim por diante.
- Armazenamento blob: fornece armazenamento em forma de ficheiro para qualquer tipo de dados.

O caderno de amostras demonstra trabalhar com mesas e bolhas, incluindo como usar uma assinatura de acesso partilhado para permitir o acesso apenas à leitura a bolhas.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Cosmos DB fornece uma loja NoSQL totalmente indexada para documentos JSON). Os seguintes artigos fornecem uma série de maneiras diferentes de trabalhar com Cosmos DB de Python:

- [Construa uma app API SQL com Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Construa uma app Flask com a API da Azure Cosmos DB para a MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Criar uma base de dados de gráficos usando Python e a API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Construa uma app Cassandra com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Construir uma app API de mesa com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com a Cosmos DB, você pode usar a biblioteca [de mesas azure-cosmosdb.](https://pypi.org/project/azure-cosmosdb-table/)

## <a name="other-azure-databases"></a>Outras bases de dados do Azure

O Azure fornece uma série de outros tipos de base de dados que pode utilizar. Os artigos abaixo fornecem orientações para o acesso às bases de dados da Python:

- [Base de Dados do Azure para PostgreSQL: utilizar o Python para se ligar e consultar dados](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início Rápido: Utilizar a Cache de Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Base de Dados do Azure para MySQL: utilizar o Python para se ligar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Copy Wizard for Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Passos seguintes

- [Como: Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md)
