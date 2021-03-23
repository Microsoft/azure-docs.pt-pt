---
title: Use o armazenamento da tabela API e a azure da Azure Cosmos usando python
description: Armazenar dados estruturados na nuvem utilizando o armazenamento da Tabela Azure ou a API da Tabela DB AZure Cosmos utilizando python.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 847e100dfaa922c359b321f17d77d2d0c67afc9b
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802867"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Introdução ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB com Python
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

O armazenamento da Azure Table e o Azure Cosmos DB são serviços que armazenam dados NoSQL estruturados na nuvem, fornecendo uma loja chave/atributo com um design sem esquemas. Uma vez que o armazenamento de Tabelas e o Azure Cosmos DB não têm esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso ao armazenamento de mesas e aos dados de API de tabela é rápido e rentável para muitos tipos de aplicações, e é tipicamente mais baixo em custo do que o SQL tradicional para volumes de dados semelhantes.

Pode utilizar o armazenamento da Tabela ou o DB Azure Cosmos para armazenar conjuntos de dados flexíveis, como dados do utilizador para aplicações web, livros de endereços, informações de dispositivos ou outros tipos de metadados que o seu serviço necessita. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

### <a name="about-this-sample"></a>Sobre este exemplo

Este exemplo mostra como utilizar o [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) em cenários comuns do armazenamento de Tabelas do Azure. O nome do SDK indica que é para ser utilizado com o Azure Cosmos DB, mas funciona quer com este serviço, quer com o armazenamento de Tabelas do Azure; cada serviço tem apenas um ponto final único. Estes cenários são explorados com exemplos de Python que ilustram como:

* Criar e eliminar tabelas
* Inserir e consultar entidades
* Modificar entidades

Ao trabalhar com os cenários deste exemplo, poderá ser útil ver [Azure Cosmos DB SDK for Python API](/python/api/overview/azure/cosmosdb) (Referência da API do SDK do Azure Cosmos DB para Python).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
* [SDK da Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Este SDK liga-se ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.
* [Conta Azure Storage](../storage/common/storage-account-create.md) ou [conta DB Azure Cosmos](https://azure.microsoft.com/try/cosmosdb/).

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Criar uma conta de armazenamento Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta da API de Tabelas do Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalar o SDK da Tabela do Azure Cosmos DB para Python

Depois de criar uma conta de Armazenamento, o próximo passo é instalar o [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Para obter detalhes sobre como instalar o SDK, veja o ficheiro [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) no repositório Cosmos DB Table SDK for Python (SDK de Tabela do Cosmos DB para Python) no GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importar as classes TableService e Entity

Para trabalhar com entidades no serviço Tabela do Azure em Python, são utilizadas as classes [TableService][py_TableService] e [Entity][py_Entity]. Adicione este código perto da parte superior do ficheiro do Python para importá-las a ambas:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Ligar ao serviço Tabela do Azure

Para ligar ao serviço Tabela de Armazenamento do Microsoft Azure, crie um objeto [TableService][py_TableService] e transmita o nome e a chave da sua conta de Armazenamento. Substitua `myaccount` e `mykey` pelo nome e a chave da conta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Ligar ao Azure Cosmos DB

Para ligar ao Azure Cosmos DB, copie a cadeia de ligação primária do portal do Azure e crie um objeto [TableService][py_TableService] com a cadeia de ligação copiada:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Criar uma tabela

Chame [create_table][py_create_table] para criar a tabela.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um objeto que represente a sua entidade e, depois, transmita-o para o [método TableService.insert_entity][py_TableService]. O objeto da entidade pode ser um dicionário ou um objeto do tipo [Entity][py_Entity] e define os nomes e os valores da propriedade da entidade. Cada entidade tem de incluir as propriedades [PartitionKey e RowKey](#partitionkey-and-rowkey) obrigatórias, para além de quaisquer outras propriedades que defina para a entidade.

Este exemplo cria um objeto de dicionário que representa uma entidade e, depois, transmite-o para o método [insert_entity][py_insert_entity] para o adicionar à tabela:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Este exemplo cria um objeto [Entity][py_Entity] e, depois, transmite-o para o método [insert_entity][py_insert_entity] para o adicionar à tabela:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey e RowKey

Tem de especificar as propriedades **PartitionKey** e **RowKey** para todas as entidades. Estes são os identificadores exclusivos das suas entidades e, juntos, formam a chave primária de uma entidade. Pode consultar com estes valores muito mais depressa do que pode consultar qualquer outra propriedade das entidades, uma vez que só essas propriedades são indexadas.

O serviço Tabela utiliza **PartitionKey** para distribuir, de forma inteligente, as entidades das tabelas por nós de armazenamento. As entidades com a mesma **PartitionKey** são armazenadas no mesmo nó. **RowKey** é o ID exclusivo da entidade na partição a que pertence.

## <a name="update-an-entity"></a>Atualizar uma entidade

Para atualizar todos os valores de propriedades de uma entidade, chame o método [update_entity][py_update_entity]. Este exemplo mostra como substituir uma entidade existente por uma versão atualizada:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Se a entidade que está a ser atualizada ainda não existir, a operação de atualização irá falhar. Se quiser armazenar uma entidade quer a mesma exista ou não, utilize [insert_or_replace_entity][py_insert_or_replace_entity]. No exemplo seguinte, a primeira chamada substituirá a entidade existente. A segunda chamada inserirá uma entidade nova, dado que não existe na tabela nenhuma entidade com as propriedades PartitionKey e RowKey especificadas.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> O método [update_entity][py_update_entity] substitui todas as propriedades e valores de entidades existentes, mas também pode utilizá-lo para remover as propriedades das mesmas. Pode utilizar o método [merge_entity][py_merge_entity] para atualizar uma entidade existente com os valores de propriedade novos ou modificados sem substituir completamente a entidade.

## <a name="modify-multiple-entities"></a>Modificar várias entidades

Para garantir o processamento atómico de um pedido por parte do serviço Tabela, pode submeter várias operações em conjunto num lote. Em primeiro lugar, utilize a classe [TableBatch][py_TableBatch] para adicionar várias operações a um único lote. Em seguida, chame [TableService][py_TableService].[ commit_batch][py_commit_batch] para submeter as operações numa operação atómica. Todas as entidades que vão ser modificadas em lote têm de estar na mesma partição.

Este exemplo adiciona duas entidades num lote:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Os lotes também podem ser utilizados com a sintaxe do gestor de contexto:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Consultar uma entidade

Para consultar uma entidade numa tabela, transmita as respetivas propriedades PartitionKey e RowKey para o método [TableService][py_TableService].[ get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Pode consultar um conjunto de entidades mediante a indicação de uma cadeia de filtro com o parâmetro **filter**. Este exemplo encontra todas as tarefas em Seattle ao aplicar um filtro em PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade

Também pode restringir as propriedades que são devolvidas para cada entidade numa consulta. Esta técnica, denominada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades ou conjuntos de resultados grandes. Utilize o parâmetro **select** e transmita os nomes das propriedades que quer devolver ao cliente.

A consulta no seguinte código devolve apenas as descrições das entidades na tabela.

> [!NOTE]
> O fragmento seguinte só funciona no Armazenamento do Azure. Não é suportado pelo Emulador de Armazenamento.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Para eliminar uma entidade, transmita as respetivas propriedades **PartitionKey** e **RowKey** para o método [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminar uma tabela

Se já não precisar de uma tabela ou de qualquer uma das entidades na mesma, chame o método [delete_table][py_delete_table] para eliminar permanentemente a tabela do Armazenamento do Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Passos seguintes

* [FAQ - Develop with the Table API](./faq.md) (FAQ - Desenvolver com a API de Tabela)
* [Azure Cosmos DB SDK for Python API reference](/python/api/overview/azure/cosmosdb) (Referência da API do SDK do Azure Cosmos DB para Python)
* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): A free, cross-platform application for working visually with Azure Storage data on Windows, macOS, and Linux (Explorador de Armazenamento do Microsoft Azure: uma aplicação multiplataformas gratuita para trabalhar visualmente com dados do Armazenamento do Azure em Windows, macOS e Linux).
* [Working with Python in Visual Studio (Windows)](/visualstudio/python/overview-of-python-tools-for-visual-studio) (Trabalhar com Python no Visual Studio [Windows])



[py_commit_batch]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_create_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_delete_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_get_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_insert_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_insert_or_replace_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_Entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity
[py_merge_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_update_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_delete_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_TableService]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice
