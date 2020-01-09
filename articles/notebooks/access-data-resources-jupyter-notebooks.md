---
title: Acessar dados em notebooks Jupyter-visualização de Azure Notebooks
description: Saiba como acessar arquivos, APIs REST, bancos de dados e diferentes recursos de armazenamento do Azure em um notebook Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646352"
---
# <a name="access-cloud-data-in-a-notebook"></a>Aceder a dados na cloud num bloco de notas

Fazer o trabalho interessante num bloco de notas do Jupyter requer que os dados. Dados, na verdade, são a alma dos blocos de notas.

Pode certamente [Importe ficheiros de dados para um projeto](work-with-project-data-files.md), mesmo usando comandos como `curl` de dentro de um bloco de notas para transferir um ficheiro diretamente. No entanto, é provável, que terá de trabalhar com dados muito mais amplo do que está disponíveis a partir de origens de não ficheiro como APIs REST, bases de dados relacionais e armazenamento, como tabelas do Azure na nuvem.

Este artigo descreve resumidamente as diferentes opções. Porque o acesso a dados é mais visto em ação, pode encontrar o código executável na [exemplos de blocos de notas do Azure - aceder aos seus dados](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

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

[Utilizar Python para consultar uma base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) fornece-lhe instruções sobre como criar uma base de dados que contém os dados da AdventureWorks e mostra como consultar dados. O mesmo código é mostrado no bloco de notas de exemplo deste artigo.

## <a name="azure-storage"></a>Armazenamento do Azure

Armazenamento do Azure fornece vários tipos diferentes de armazenamento não relacionais, dependendo do tipo de dados que tiver e como precisa aceder à mesma:

- Armazenamento de tabelas: fornece armazenamento de elevado volume e de baixo custo para dados em tabela, como registos de sensor recolhidos, os registos de diagnóstico e assim por diante.
- Armazenamento de BLOBs: fornece armazenamento de ficheiros semelhantes para qualquer tipo de dados.

O bloco de notas de exemplo demonstra a trabalhar com tabelas e blobs, incluindo como utilizar uma assinatura de acesso partilhado para permitir o acesso só de leitura de blobs.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB fornece um arquivo de NoSQL totalmente indexado para documentos JSON). Os seguintes artigos fornecem um número de maneiras diferentes de trabalhar com o Cosmos DB a partir de Python:

- [Aplicação de compilação de uma API de SQL com Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Compilar um aplicativo Flask com a API do Azure Cosmos DB para MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Criar uma base de dados com Python e a API do Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Criar uma aplicação do Cassandra com o Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Criar uma aplicação de API com Python e Azure Cosmos DB tabela](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com o Cosmos DB, pode utilizar o [azure-cosmos DB-table](https://pypi.org/project/azure-cosmosdb-table/) biblioteca.

## <a name="other-azure-databases"></a>Outros bancos de dados do Azure

O Azure fornece uma série de outros tipos de base de dados que pode utilizar. Os artigos abaixo fornecem orientações para aceder a essas bases de dados a partir de Python:

- [Base de dados do Azure para PostgreSQL: utilizar o Python para ligar e consultar dados](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início rápido: Utilizar a Cache de Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Base de dados do Azure para MySQL: utilizar o Python para ligar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Assistente para copiar para a fábrica de dados do Azure](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Passos seguintes

- [Como: trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
