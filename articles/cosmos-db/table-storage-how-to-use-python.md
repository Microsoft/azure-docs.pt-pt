---
title: Introdução ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB com Python
description: Armazene dados estruturados na cloud com o armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0f0acc721fd8888953d80976234b431943985ebf
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356278"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Introdução ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB com Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

O armazenamento de Tabelas do Azure e o Azure Cosmos DB são serviços que armazenam dados NoSQL estruturados na cloud, proporcionando u arquivo de atributos/chaves com um design sem esquemas. Uma vez que o armazenamento de Tabelas e o Azure Cosmos DB não têm esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso aos dados do armazenamento de Tabelas e da API de Tabela é rápido e rentável para muitos tipos de aplicações e, normalmente, é mais económico do que o SQL tradicional para volumes de dados semelhantes.

Pode utilizar o armazenamento de Tabelas ou o Azure Cosmos DB para armazenar conjuntos de dados flexíveis, como os dados do utilizador para aplicações Web, livros de endereços, informações do dispositivo ou outros tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

### <a name="about-this-sample"></a>Sobre este exemplo
Este exemplo mostra como utilizar o [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) em cenários comuns do armazenamento de Tabelas do Azure. O nome do SDK indica que é para ser utilizado com o Azure Cosmos DB, mas funciona quer com este serviço, quer com o armazenamento de Tabelas do Azure; cada serviço tem apenas um ponto final único. Estes cenários são explorados com exemplos de Python que ilustram como:
* Criar e eliminar tabelas
* Inserir e consultar entidades
* Modificar entidades

Ao trabalhar com os cenários deste exemplo, poderá ser útil ver [Azure Cosmos DB SDK for Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) (Referência da API do SDK do Azure Cosmos DB para Python).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 ou 3.6
- [SDK da Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Este SDK liga-se ao armazenamento de Tabelas do Azure e à API de Tabela do Azure Cosmos DB.
- [Conta de Armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) ou [conta do Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalar o SDK da Tabela do Azure Cosmos DB para Python

Depois de criar uma conta de Armazenamento, o próximo passo é instalar o [SDK de Tabela do Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Para obter detalhes sobre como instalar o SDK, veja o ficheiro [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) no repositório Cosmos DB Table SDK for Python (SDK de Tabela do Cosmos DB para Python) no GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importar as classes TableService e Entity

Para trabalhar com entidades no serviço tabela do Azure no Python, use as classes [TableService][py_TableService] and [Entity][py_Entity] . Adicione este código perto da parte superior do ficheiro do Python para importá-las a ambas:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Ligar ao serviço Tabela do Azure

Para ligar ao serviço Tabela de Armazenamento do Azure, crie um objeto [TableService][py_TableService] e transmita o nome e a chave da sua conta de Armazenamento. Substitua `myaccount` e `mykey` pelo nome e a chave da conta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Ligar ao Azure Cosmos DB

Para ligar ao Azure Cosmos DB, copie a cadeia de ligação principal a partir do portal do Azure e crie um objeto [TableService][py_TableService] com a cadeia de ligação copiada:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Criar uma tabela

Chame [CREATE_TABLE][py_create_table] para criar a tabela.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto que represente sua entidade e, em seguida, passe o objeto para o. The entity object can be a dictionary or an object of type [Entity][py_Entity] [método TableService. insert_entity][py_TableService]e defina os nomes e valores de propriedade da sua entidade. Cada entidade tem de incluir as propriedades [PartitionKey e RowKey](#partitionkey-and-rowkey) obrigatórias, para além de quaisquer outras propriedades que defina para a entidade.

Este exemplo cria um objeto Dictionary que representa uma entidade e, em seguida, passa-o para o método [insert_entity][py_insert_entity] para adicioná-lo à tabela:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Este exemplo cria um método de [entidade][py_Entity] object, then passes it to the [insert_entity][py_insert_entity] para adicioná-lo à tabela:

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

Para atualizar todos os valores de propriedade de uma entidade, chame o método [update_entity][py_update_entity] . Este exemplo mostra como substituir uma entidade existente por uma versão atualizada:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Se a entidade que está a ser atualizada ainda não existir, a operação de atualização irá falhar. Se você quiser armazenar uma entidade, quer ela exista ou não, use [insert_or_replace_entity][py_insert_or_replace_entity]. No exemplo seguinte, a primeira chamada substituirá a entidade existente. A segunda chamada inserirá uma entidade nova, dado que não existe na tabela nenhuma entidade com as propriedades PartitionKey e RowKey especificadas.

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
> O método [update_entity][py_update_entity] substitui todas as propriedades e valores de uma entidade existente, que também pode ser usada para remover propriedades de uma entidade existente. Você pode usar o método [merge_entity][py_merge_entity] para atualizar uma entidade existente com valores de propriedade novos ou modificados sem substituir completamente a entidade.

## <a name="modify-multiple-entities"></a>Modificar várias entidades

Para garantir o processamento atómico de um pedido por parte do serviço Tabela, pode submeter várias operações em conjunto num lote. Primeiro, use a classe [TableBatch][py_TableBatch] para adicionar várias operações a um único lote. Em seguida, chame [TableService][py_TableService]. [commit_batch][py_commit_batch] para enviar as operações em uma operação atômica. Todas as entidades que vão ser modificadas em lote têm de estar na mesma partição.

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

Para consultar uma entidade em uma tabela, passe seu PartitionKey e RowKey para o [TableService][py_TableService]. método [get_entity][py_get_entity] .

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
> O fragmento seguinte só funciona no Armazenamento do Azure. Não é suportado pelo emulador de armazenamento.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Exclua uma entidade passando seu **PartitionKey** e **RowKey** para o método [delete_entity][py_delete_entity] .

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminar uma tabela

Se você não precisar mais de uma tabela ou de qualquer uma das entidades dentro dela, chame o método [delete_table][py_delete_table] para excluir permanentemente a tabela do armazenamento do Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Passos Seguintes

* [FAQ - Develop with the Table API](https://docs.microsoft.com/azure/cosmos-db/faq) (FAQ - Desenvolver com a API de Tabela)
* [Azure Cosmos DB SDK for Python API reference](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) (Referência da API do SDK do Azure Cosmos DB para Python)
* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md): Um aplicativo gratuito de plataforma cruzada para trabalhar visualmente com os dados do armazenamento do Azure no Windows, no macOS e no Linux.
* [Working with Python in Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio) (Trabalhar com Python no Visual Studio [Windows])


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
