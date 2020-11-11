---
title: Use o armazenamento da mesa Azure e a API da mesa DB AZure Cosmos com C++
description: Armazenar dados estruturados na nuvem utilizando o armazenamento da Tabela Azure ou a API da Tabela DB AZure Cosmos utilizando C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489884"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Como utilizar o Armazenamento de Tabelas do Azure e a API de Tabela do Azure Cosmos DB com C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este guia mostra-lhe cenários comuns utilizando o serviço de armazenamento da Mesa Azure ou a AZure Cosmos DB Table API. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Este artigo abrange os seguintes cenários:

* Criar e apagar uma tabela
* Trabalhar com entidades de mesa

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A versão recomendada é Storage Client Library 2.2.0, que está disponível através da utilização [de NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Criar contas

### <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++

Neste guia, utiliza funcionalidades de armazenamento a partir de uma aplicação C++. Para tal, instale a Biblioteca do Cliente de Armazenamento Azure para C++.

Para instalar a Biblioteca do Cliente de Armazenamento Azure para C++, utilize os seguintes métodos:

* **Linux:** Siga as instruções dadas na Biblioteca do [Cliente de Armazenamento Azure para C++ README: Começar na página Linux.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Janelas:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como gestor de dependência. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar vcpkg. Em seguida, utilize o seguinte comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Pode encontrar um guia para como construir o código fonte e exportar para Nuget no ficheiro [README.](https://github.com/Azure/azure-storage-cpp#download--install)

### <a name="configure-access-to-the-table-client-library"></a>Configurar o acesso à biblioteca de cliente de tabela

Para utilizar as APIs de armazenamento Azure para aceder às tabelas, adicione as `include` seguintes declarações ao topo do ficheiro C++:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Um cliente do Armazenamento do Azure ou cliente de do Cosmos DB utiliza uma cadeia de ligação para armazenar pontos finais e credenciais para aceder aos serviços de gestão de dados. Quando executar uma aplicação do cliente, deve fornecer a cadeia de ligação de armazenamento ou a cadeia de ligação DB Azure Cosmos no formato apropriado.

### <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Armazenamento do Microsoft Azure

Este exemplo mostra como declarar um campo estático para segurar a cadeia de ligação Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Utilize o nome da sua conta de Armazenamento para `<your_storage_account>` . Para <your_storage_account_key>, utilize a chave de acesso para a conta de Armazenamento listada no [portal Azure](https://portal.azure.com). Para obter informações sobre contas de Armazenamento e chaves de acesso, consulte [Criar uma conta de armazenamento.](../storage/common/storage-account-create.md)

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Configurar uma cadeia de ligação Azure Cosmos DB

Este exemplo mostra como declarar um campo estático para segurar a cadeia de ligação Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Use o nome da sua conta Azure Cosmos DB para `<your_cosmos_db_account>` . Introduza a sua chave primária para `<your_cosmos_db_account_key>` . Introduza o ponto final listado no [portal Azure](https://portal.azure.com) para `<your_cosmos_db_endpoint>` .

Para testar a sua aplicação no seu computador local baseado no Windows, pode utilizar o Emulador de Armazenamento Azure que está instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O Emulador de Armazenamento é um utilitário que simula os serviços de Azure Blob, Queue e Table disponíveis na sua máquina de desenvolvimento local. O exemplo a seguir mostra como declarar um campo estático para segurar a cadeia de ligação ao seu emulador de armazenamento local:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o Emulador de Armazenamento Azure, a partir do seu ambiente de trabalho Windows, selecione o botão **Iniciar** ou a tecla Windows. Introduza e execute o *Emulador de Armazenamento Microsoft Azure*. Para obter mais informações, consulte [o Emulador de Armazenamento Azure para desenvolvimento e testes.](../storage/common/storage-use-emulator.md)

### <a name="retrieve-your-connection-string"></a>Obter a sua cadeia de ligação

Pode utilizar a `cloud_storage_account` aula para representar as informações da sua conta de armazenamento. Para obter as informações da sua conta de armazenamento a partir da cadeia de ligação de armazenamento, utilize o `parse` método.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, faça uma referência a uma `cloud_table_client` aula. Esta classe permite obter objetos de referência para tabelas e entidades armazenadas dentro do serviço de armazenamento de mesa. O seguinte código cria um `cloud_table_client` objeto utilizando o objeto da conta de armazenamento que recuperou anteriormente:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Criar e adicionar entidades a uma mesa

### <a name="create-a-table"></a>Criar uma tabela

Um `cloud_table_client` objeto permite obter objetos de referência para tabelas e entidades. O código a seguir cria um `cloud_table_client` objeto e utiliza-o para criar uma nova tabela.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma mesa, crie um novo `table_entity` objeto e passe-o para `table_operation::insert_entity` . O código seguinte utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser questionadas mais rapidamente do que as entidades com diferentes chaves de partição. A utilização de teclas de partição diversificadas permite uma maior escalabilidade paralela ao funcionamento. Para obter mais informações, consulte a [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../storage/blobs/storage-performance-checklist.md).

O código a seguir cria uma nova instância `table_entity` com alguns dados do cliente para armazenar. O código em seguida chama `table_operation::insert_entity` para criar um objeto para inserir uma entidade em uma `table_operation` mesa, e associa a nova entidade de tabela com ele. Finalmente, o código chama o `execute` método do `cloud_table` objeto. O novo `table_operation` envia um pedido ao serviço mesa para inserir a nova entidade cliente na `people` tabela.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Pode inserir um lote de entidades no serviço Tabela numa operação de escrita. O código a seguir cria um `table_batch_operation` objeto e, em seguida, adiciona-lhe três operações de inserção. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo os seus valores e, em seguida, chamando `insert` o método no objeto para associar a entidade a uma nova `table_batch_operation` operação de inserção. Depois, o código chama `cloud_table.execute` para executar a operação.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Algumas coisas a salientar nas operações de lote:

* Você pode fazer até 100 `insert` , , , , , e `delete` `merge` `replace` `insert-or-merge` `insert-or-replace` operações em qualquer combinação em um único lote.  
* Uma operação de lote pode ter uma operação de recuperação, se for a única operação no lote.  
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.  
* Uma operação em lote está limitada a um payload de dados de 4 MB.  

## <a name="query-and-modify-entities"></a>Consulta e modificação de entidades

### <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Para consultar uma tabela para todas as entidades numa divisória, use um `table_query` objeto. O exemplo de código a seguir especifica um filtro para entidades onde `Smith` está a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.  

> [!NOTE]
> Estes métodos não são atualmente suportados para C++ no Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

A consulta neste exemplo devolve todas as entidades que correspondem aos critérios do filtro. Se tem tabelas grandes e tem de transferir as entidades de tabela muitas vezes, recomendamos que armazene os dados em blobs de armazenamento do Azure em vez disso.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades numa partição

Se não quiser consultar todas as entidades numa partição, pode especificar um intervalo. Combine o filtro da chave de partição com um filtro de chave de linha. O exemplo de código a seguir utiliza dois filtros para obter todas as entidades em partição `Smith` onde a chave de linha (primeiro nome) começa com uma letra mais cedo do que no `E` alfabeto, e depois imprime os resultados da consulta.  

> [!NOTE]
> Estes métodos não são atualmente suportados para C++ no Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Obter uma única entidade

Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza `table_operation::retrieve_entity` para especificar o `Jeff Smith` cliente. Este método devolve apenas uma entidade, em vez de uma coleção, e o valor devolvido está em `table_result` . Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Substituir uma entidade

Para substituir uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone e o endereço de e-mail de um cliente existente. Em vez de `table_operation::insert_entity` chamar, este código usa `table_operation::replace_entity` . Esta abordagem faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha mudado desde que foi recuperada. Se tiver sido alterada, a operação falha. Esta falha impede que a sua aplicação sobreescreva uma alteração feita entre a recuperação e a atualização por outro componente. O manuseamento adequado desta falha é recuperar a entidade novamente, fazer as suas alterações, se ainda válidas, e depois fazer outra `table_operation::replace_entity` operação.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade

`table_operation::replace_entity` as operações falham se a entidade tiver sido alterada desde que foi recuperada do servidor. Além disso, deve recuperar a entidade do servidor primeiro para `table_operation::replace_entity` ter sucesso. Às vezes, não se sabe se a entidade existe no servidor. Os valores atuais armazenados nele são irrelevantes, porque a sua atualização deve sobrepor-se a todos. Para realizar este resultado, utilize uma `table_operation::insert_or_replace_entity` operação. Esta operação insere a entidade se não existir. A operação substitui a entidade se existir. No seguinte exemplo de código, a entidade do cliente `Jeff Smith` ainda é recuperada, mas depois é guardada de volta para o servidor utilizando `table_operation::insert_or_replace_entity` . Todas as atualizações efetuadas à entidade entre as operações de obtenção e atualização serão substituídas.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. A consulta no código que se segue utiliza o `table_query::set_select_columns` método para devolver apenas os endereços de e-mail das entidades na tabela.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Consultar algumas propriedades de uma entidade é uma operação mais eficiente do que obter todas as propriedades.
>

## <a name="delete-content"></a>Eliminar conteúdo

### <a name="delete-an-entity"></a>Eliminar uma entidade

Pode apagar uma entidade depois de a recuperar. Depois de recuperar uma entidade, ligue `table_operation::delete_entity` para a entidade para apagar. Então chame o `cloud_table.execute` método. O código seguinte recupera e elimina uma entidade com uma chave de partição `Smith` e uma chave de linha de `Jeff` .

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Eliminar uma tabela

Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Uma tabela que foi eliminada não está disponível para ser recriada durante algum tempo após a eliminação.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Resolução de Problemas

Para a Edição Comunitária do Estúdio Visual, se o seu projeto obtiver erros de construção por causa dos ficheiros incluídos *storage_account.h* e *table.h,* remova o interruptor **/permissivo-compilador:**

1. No **Explorador de Soluções** , clique com o botão direito do rato no seu projeto e selecione **Propriedades**.
1. Na caixa de diálogo **Páginas de Propriedades** , expanda **Propriedades de Configuração** , expanda **C/C++** e selecione **Idioma**.
1. Definir **Modo de conformidade** para **Não**.

## <a name="next-steps"></a>Passos seguintes

O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

Siga estas ligações para saber mais sobre o Armazenamento do Microsoft Azure e a API de tabela do Azure Cosmos DB:

* [Introdução à API da Tabela](table-introduction.md)
* [Listar Recursos do Armazenamento do Microsoft Azure em C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referência da Biblioteca de Cliente do Armazenamento para C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
