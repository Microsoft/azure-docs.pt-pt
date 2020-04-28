---
title: Dados de acesso em cadernos Jupyter - Pré-visualização de cadernos Azure
description: Saiba como aceder a ficheiros, APIs REST, bases de dados e diferentes recursos de Armazenamento Azure a partir de um caderno Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646352"
---
# <a name="access-cloud-data-in-a-notebook"></a>Aceder a dados da cloud num bloco de notas

Fazer um trabalho interessante num caderno jupyter requer dados. Data, na verdade, é o sangue vital dos cadernos.

Pode certamente [importar ficheiros](work-with-project-data-files.md)de dados para `curl` um projeto , mesmo usando comandos como dentro de um caderno para descarregar um ficheiro diretamente. É provável, no entanto, que precise de trabalhar com dados muito mais extensos que estão disponíveis a partir de fontes não-ficheiros, como APIs rest, bases de dados relacionais e armazenamento em nuvem, como tabelas Azure.

Este artigo descreve brevemente estas diferentes opções. Como o acesso aos dados é melhor visto em ação, pode encontrar código runnável nas Amostras de [Cadernos Azure - Aceda](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)aos seus dados .

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>APIs REST

De um modo geral, a grande quantidade de dados disponíveis na Internet é acedida não através de ficheiros, mas através de APIs REST. Felizmente, como uma célula de caderno pode conter qualquer código que você quiser, você pode usar código para enviar pedidos e receber dados JSON. Em seguida, pode convertê-lo em qualquer formato que queira utilizar, como um quadro de dados pandas.

Para aceder a dados utilizando uma API REST, utilize o mesmo código nas células de código de um caderno que utiliza em qualquer outra aplicação. A estrutura geral que utiliza a biblioteca de pedidos é a seguinte:

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

## <a name="azure-sql-databases"></a>Bases de dados Azure SQL

Pode aceder às bases de dados do SQL Server com a assistência das bibliotecas pyodbc ou pymssql.

Use python para consultar uma base de [dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) dá-lhe instruções sobre a criação de uma base de dados contendo dados adventureWorks, e mostra como consultar esses dados. O mesmo código é mostrado no caderno de amostras para este artigo.

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage fornece vários tipos diferentes de armazenamento não relacional, dependendo do tipo de dados que tem e de como precisa aceder a ele:

- Armazenamento de mesa: fornece armazenamento de baixo custo e de alto volume para dados tabulares, tais como registos de sensores recolhidos, registos de diagnóstico, e assim por diante.
- Armazenamento blob: fornece armazenamento semelhante a ficheiro para qualquer tipo de dados.

O caderno de amostras demonstra trabalhar com mesas e bolhas, incluindo como usar uma assinatura de acesso partilhado para permitir o acesso apenas a bolhas.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Cosmos DB fornece uma loja NoSQL totalmente indexada para documentos JSON). Os seguintes artigos fornecem uma série de maneiras diferentes de trabalhar com cosmos DB de Python:

- [Construa uma aplicação SQL API com Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Construa uma aplicação de Flask com a API do Azure Cosmos DB para o MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Crie uma base de dados de gráficos usando Python e a API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Construa uma app Cassandra com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Construa uma app Table API com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com cosmos DB, você pode usar a biblioteca [de mesa azure-cosmosdb.](https://pypi.org/project/azure-cosmosdb-table/)

## <a name="other-azure-databases"></a>Outras bases de dados Azure

O Azure fornece uma série de outros tipos de bases de dados que pode utilizar. Os artigos abaixo fornecem orientações para aceder às bases de dados da Python:

- [Base de Dados do Azure para PostgreSQL: utilizar o Python para se ligar e consultar dados](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início Rápido: Utilizar a Cache de Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Base de Dados do Azure para MySQL: utilizar o Python para se ligar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Assistente de cópia para fábrica de dados azure](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Passos seguintes

- [Como: Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md)
