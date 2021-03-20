---
title: Use Azure Cosmos DB Table API e Azure Table Storage com Ruby
description: Armazene dados estruturados na cloud com o armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 2d0c8433fff58854cb77a4e806058eae1937e71b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101124"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Como utilizar o Armazenamento de Tabelas do Azure e a API de Tabela do Azure Cosmos com Ruby
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este artigo mostra-lhe como criar tabelas, armazenar os seus dados e realizar operações CRUD nos dados. Escolha o serviço Azure Table ou a API da Tabela DB AZure Cosmos. As amostras descritas neste artigo estão escritas em Ruby e utilizam a Biblioteca de [Clientes de Mesa de Armazenamento Azure para a Ruby.](https://github.com/azure/azure-storage-ruby/tree/master/table) Os cenários abrangidos incluem criar uma tabela, apagar uma tabela, inserir entidades e consultar entidades da tabela.

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Criar uma conta de armazenamento Azure**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Criar uma conta do Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-azure-storage-or-azure-cosmos-db"></a>Adicione acesso ao armazenamento Azure ou Azure Cosmos DB

Para utilizar o Armazenamento do Azure ou o Azure Cosmos DB, tem de transferir e utilizar o pacote do Azure do Ruby que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST de Tabela.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva **gem install azure-storage-table** na janela de comandos, para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Utilize o seu editor de texto favorito e adicione o seguinte à parte superior do ficheiro Ruby em que pretende utilizar o Armazenamento:

```ruby
require "azure/storage/table"
```

## <a name="add-your-connection-string"></a>Adicione a sua cadeia de conexão

Pode ligar-se à conta de armazenamento Azure ou à conta AZure Cosmos DB Table API. Obtenha a cadeia de ligação com base no tipo de conta que está a utilizar.

### <a name="add-an-azure-storage-connection"></a>Adicionar uma ligação ao Armazenamento do Azure

O módulo de Armazenamento do Azure lê as variáveis de ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** para obter as informações necessárias para ligar à sua conta de Armazenamento do Azure. Se estas variáveis de ambiente não estiverem definidas, tem de especificar as informações de conta antes de utilizar **Azure::Storage::Table::TableService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue para a Conta de armazenamento que pretende utilizar.
3. No painel Definições à direita, clique em **Chaves de Acesso**.
4. No painel Chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

### <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma ligação do Azure Cosmos DB

Para ligar ao Azure Cosmos DB, copie a cadeia de ligação principal do portal do Azure e crie um objeto **Cliente** através da cadeia de ligação copiada. Pode passar o objeto **Cliente** ao criar um objeto **TableService**:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Criar uma tabela

O objeto **Azure::Storage::Table::TableService** permite-lhe trabalhar com tabelas e entidades. Para criar uma tabela, utilize o método **create_table()**. O exemplo seguinte cria uma tabela ou imprime o erro, caso exista algum.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, primeiro crie um objeto de hash que define as propriedades da sua entidade. Tenha em atenção que para cada entidade tem de especificar uma **PartitionKey** e uma **RowKey**. Estes são os identificadores exclusivos das suas entidades e os valores que podem ser consultados muito mais rapidamente do que as outras propriedades. O Armazenamento do Azure utiliza a **PartitionKey** para distribuir automaticamente as entidades da tabela através de vários nós de armazenamento. As entidades com a mesma **PartitionKey** são armazenadas no mesmo nó. O **RowKey** é o ID exclusivo da entidade na partição a que pertence.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Atualizar uma entidade

Existem vários métodos disponíveis para atualizar uma entidade existente:

* **update_entity():** atualiza uma entidade existente ao substitui-la.
* **merge_entity():** atualiza uma entidade existente ao intercalar novos valores de propriedade na entidade existente.
* **insert_or_merge_entity():** atualiza uma entidade existente ao substitui-la. Se não existir uma entidade, será inserida uma nova:
* **insert_or_replace_entity():** atualiza uma entidade existente ao intercalar novos valores de propriedade na entidade existente. Se não existir nenhuma entidade, será inserida uma nova.

O exemplo seguinte demonstra como atualizar uma entidade com **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Com **update_entity()** e **merge_entity()**, se a entidade que está a atualizar não existir, então a operação de atualização irá falhar. Por conseguinte, se pretender armazenar uma entidade, independentemente de já existir, deve, em vez disso, utilizar **insert_or_replace_entity()** ou **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Por vezes, é útil submeter várias operações em conjunto num batch para garantir um processamento atómico pelo servidor. Para tal, crie primeiro um objeto de **Batch** e, em seguida, utilize o método **execute_batch()** no **TableService**. O exemplo seguinte demonstra como submeter duas entidades com RowKey 2 e 3 num batch. Tenha em atenção que funciona apenas para entidades com o mesmo PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Consultar uma entidade

Para consultar uma entidade numa tabela, utilize o método **get_entity()**, ao passar o nome da tabela, **PartitionKey** e **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Consultar um conjunto de entidades

Para consultar um conjunto de entidades numa tabela, crie um objeto de hash de consulta e utilize o método **query_entities()**. O exemplo seguinte demonstra como obter todas as entidades com a mesma **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Se o conjunto de resultados for demasiado grande para uma única consulta devolver, é devolvido um token de continuação que pode utilizar para obter as páginas subsequentes.


## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. Esta técnica, denominada "projeção", reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para grandes entidades. Utilize a cláusula selecionada e passe os nomes das propriedades que pretende levar ao cliente.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Para eliminar uma entidade, utilize o método **delete_entity()**. Passe o nome da tabela que contém a entidade, a PartitionKey e a RowKey da entidade.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Eliminar uma tabela

Para eliminar uma tabela, utilize o método **delete_table()** e passe o nome da tabela que pretende eliminar.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Passos seguintes

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [Centro de Programadores do Ruby](https://azure.microsoft.com/develop/ruby/)
* [Biblioteca de Cliente das Tabelas de Armazenamento do Microsoft Azure para Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table)