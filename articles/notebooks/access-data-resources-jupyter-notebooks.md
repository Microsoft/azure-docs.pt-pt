---
title: Aceder a recursos de dados de blocos de notas do Jupyter no Azure
description: Como aceder a ficheiros, REST APIs, bases de dados e diferentes recursos de armazenamento do Azure a partir de um bloco de notas do Jupyter.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 23acdf99f6cb69f100e484e236580f3b2b43ba94
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277687"
---
# <a name="access-cloud-data-in-a-notebook"></a>Aceder a dados na cloud num bloco de notas

Fazer o trabalho interessante num bloco de notas do Jupyter requer que os dados. Dados, na verdade, são a alma dos blocos de notas.

Certamente, você pode [importar arquivos de dados para um projeto](work-with-project-data-files.md), mesmo usando comandos como `curl` de dentro de um bloco de anotações para baixar um arquivo diretamente. No entanto, é provável, que terá de trabalhar com dados muito mais amplo do que está disponíveis a partir de origens de não ficheiro como APIs REST, bases de dados relacionais e armazenamento, como tabelas do Azure na nuvem.

Este artigo descreve resumidamente as diferentes opções. Como o acesso a dados é mais bem visto em ação, você pode encontrar código executável no [Azure notebooks amostras – acessar seus dados](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>APIs REST

Em termos gerais, a vasta quantidade de dados disponíveis a partir da Internet é acessada não por meio de arquivos, mas através de REST APIs. Felizmente, uma vez que uma célula de bloco de notas pode conter qualquer código que quiser, pode usar código para enviar pedidos e receber dados JSON. Em seguida, pode converter esse JSON em qualquer formato que pretende utilizar, por exemplo, um pandas dataframe.

Para acessar dados usando uma API REST, utilize o mesmo código em células de código de um bloco de notas que utilizar em qualquer outro aplicativo. A estrutura geral usando a biblioteca de pedidos é o seguinte:

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

## <a name="azure-sql-databases"></a>Bases de dados SQL do Azure

Pode acessar bancos de dados SQL com o auxílio de bibliotecas do pyodbc ou pymssql.

[Usar o Python para consultar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) fornece instruções sobre a criação de um banco de dado que contém data AdventureWorks e mostra como consultar esses dados. O mesmo código é mostrado no bloco de notas de exemplo deste artigo.

## <a name="azure-storage"></a>Storage do Azure

Armazenamento do Azure fornece vários tipos diferentes de armazenamento não relacionais, dependendo do tipo de dados que tiver e como precisa aceder à mesma:

- Armazenamento de tabelas: fornece armazenamento de elevado volume e de baixo custo para dados em tabela, como registos de sensor recolhidos, os registos de diagnóstico e assim por diante.
- Armazenamento de BLOBs: fornece armazenamento de ficheiros semelhantes para qualquer tipo de dados.

O bloco de notas de exemplo demonstra a trabalhar com tabelas e blobs, incluindo como utilizar uma assinatura de acesso partilhado para permitir o acesso só de leitura de blobs.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB fornece um arquivo de NoSQL totalmente indexado para documentos JSON). Os seguintes artigos fornecem um número de maneiras diferentes de trabalhar com o Cosmos DB a partir de Python:

- [Criar um aplicativo de API do SQL com Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Compilar um aplicativo Flask com a API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Criar um banco de dados de grafo usando Python e a API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Criar um aplicativo Cassandra com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Criar um aplicativo API de Tabela com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com Cosmos DB, você pode usar a biblioteca [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Outros bancos de dados do Azure

O Azure fornece uma série de outros tipos de base de dados que pode utilizar. Os artigos abaixo fornecem orientações para aceder a essas bases de dados a partir de Python:

- [Banco de dados do Azure para PostgreSQL: usar o Python para se conectar e consultar o dado](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início rápido: usar o cache Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Banco de dados do Azure para MySQL: usar o Python para conectar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Assistente de cópia para Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Passos seguintes

- [Como trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
