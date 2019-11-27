---
title: Como utilizar o armazenamento de tabelas do Azure e a API de tabela do Azure Cosmos DB com C++
description: Armazene dados estruturados na nuvem utilizando o Armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: b8fa0a3cebd87f4da1a47c605ba21b0cb10a2517
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220055"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Como utilizar o Armazenamento de Tabelas do Azure e a API de Tabela do Azure Cosmos DB com C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Este guia mostra cenários comuns usando o serviço de armazenamento de tabelas do Azure ou Azure Cosmos DB API de Tabela. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Este artigo aborda os seguintes cenários:

* Criar e excluir uma tabela
* Trabalhar com entidades de tabela

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A versão recomendada é a biblioteca de cliente de armazenamento 2.2.0, que está disponível usando [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Criar contas

### <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++

Neste guia, você usa os recursos de armazenamento de C++ um aplicativo. Para fazer isso, instale a biblioteca de cliente do armazenamento C++do Azure para.

Para instalar a biblioteca de cliente de armazenamento C++do Azure para, use os seguintes métodos:

* **Linux:** Siga as instruções fornecidas na página [biblioteca de cliente do armazenamento C++ do Azure para leiame: introdução no Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) .
* **Windows:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como o Gerenciador de dependência. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar o vcpkg. Em seguida, use o seguinte comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Você pode encontrar um guia sobre como criar o código-fonte e exportar para o NuGet no arquivo [Leiame](https://github.com/Azure/azure-storage-cpp#download--install) .

### <a name="configure-access-to-the-table-client-library"></a>Configurar o acesso à biblioteca de cliente de tabela

Para usar as APIs de armazenamento do Azure para acessar tabelas, adicione as seguintes instruções de `include` à parte C++ superior do arquivo:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Um cliente do Armazenamento do Azure ou cliente de do Cosmos DB utiliza uma cadeia de ligação para armazenar pontos finais e credenciais para aceder aos serviços de gestão de dados. Ao executar um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento ou Azure Cosmos DB cadeia de conexão no formato apropriado.

### <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Armazenamento do Microsoft Azure

Este exemplo mostra como declarar um campo estático para conter a cadeia de conexão do armazenamento do Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Use o nome da sua conta de armazenamento para `<your_storage_account>`. Para < your_storage_account_key >, use a chave de acesso para a conta de armazenamento listada no [portal do Azure](https://portal.azure.com). Para obter informações sobre contas de armazenamento e chaves de acesso, consulte [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Configurar uma cadeia de ligação Azure Cosmos DB

Este exemplo mostra como declarar um campo estático para manter a cadeia de conexão Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Use o nome da sua conta de Azure Cosmos DB para `<your_cosmos_db_account>`. Insira sua chave primária para `<your_cosmos_db_account_key>`. Insira o ponto de extremidade listado no [portal do Azure](https://portal.azure.com) para `<your_cosmos_db_endpoint>`.

Para testar seu aplicativo em seu computador local baseado no Windows, você pode usar o emulador de armazenamento do Azure que é instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços tabela, fila e blob do Azure disponíveis no computador de desenvolvimento local. O exemplo a seguir mostra como declarar um campo estático para conter a cadeia de conexão para o emulador de armazenamento local:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento do Azure, na área de trabalho do Windows, selecione o botão **Iniciar** ou a tecla Windows. Insira e execute *emulador de armazenamento do Microsoft Azure*. Para obter mais informações, veja [Utilizar o emulador de armazenamento do Azure para programação e teste](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Obter a cadeia de ligação

Você pode usar a classe `cloud_storage_account` para representar as informações da conta de armazenamento. Para recuperar as informações da conta de armazenamento da cadeia de conexão de armazenamento, use o método `parse`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma classe `cloud_table_client`. Essa classe permite que você obtenha objetos de referência para tabelas e entidades armazenadas no serviço de armazenamento de tabela. O código a seguir cria um objeto `cloud_table_client` usando o objeto de conta de armazenamento que você recuperou anteriormente:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Criar e adicionar entidades a uma tabela

### <a name="create-a-table"></a>Criar uma tabela

Um objeto `cloud_table_client` permite que você obtenha objetos de referência para tabelas e entidades. O código a seguir cria um objeto `cloud_table_client` e o usa para criar uma nova tabela.

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

Para adicionar uma entidade a uma tabela, crie um novo objeto `table_entity` e passe-o para `table_operation::insert_entity`. O código seguinte utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que as entidades com chaves de partição diferentes. O uso de chaves de partição diversas permite uma escalabilidade de operação paralela maior. Para obter mais informações, consulte a [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../storage/common/storage-performance-checklist.md).

O código a seguir cria uma nova instância do `table_entity` com alguns dados do cliente para armazenar. O próximo código chama `table_operation::insert_entity` para criar um objeto `table_operation` para inserir uma entidade em uma tabela e associa a nova entidade de tabela a ela. Por fim, o código chama o método `execute` no objeto `cloud_table`. O novo `table_operation` envia uma solicitação ao serviço tabela para inserir a nova entidade Customer na tabela `people`.  

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

Pode inserir um lote de entidades no serviço Tabela numa operação de escrita. O código a seguir cria um objeto `table_batch_operation` e, em seguida, adiciona três operações INSERT a ele. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores e, em seguida, chamando o método `insert` no objeto `table_batch_operation` para associar a entidade a uma nova operação de inserção. Em seguida, o código chama `cloud_table.execute` para executar a operação.  

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

* Você pode fazer até 100 `insert`, `delete`, `merge`, `replace`, `insert-or-merge`e `insert-or-replace` operações em qualquer combinação em um único lote.  
* Uma operação em lote pode ter uma operação de recuperação, se for a única operação no lote.  
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.  
* Uma operação em lote está limitada a um payload de dados de 4 MB.  

## <a name="query-and-modify-entities"></a>Consultar e modificar entidades

### <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Para consultar uma tabela para todas as entidades em uma partição, use um objeto `table_query`. O exemplo de código a seguir especifica um filtro para entidades em que `Smith` é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.  

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

A consulta neste exemplo retorna todas as entidades que correspondem aos critérios de filtro. Se tem tabelas grandes e tem de transferir as entidades de tabela muitas vezes, recomendamos que armazene os dados em blobs de armazenamento do Azure em vez disso.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades numa partição

Se você não quiser consultar todas as entidades em uma partição, poderá especificar um intervalo. Combine o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição `Smith` em que a chave de linha (primeiro nome) começa com uma letra anterior à `E` no alfabeto e, em seguida, imprime os resultados da consulta.  

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

Pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa `table_operation::retrieve_entity` para especificar o `Jeff Smith`do cliente. Esse método retorna apenas uma entidade, em vez de uma coleção, e o valor retornado está em `table_result`. Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.  

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

Para substituir uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone e o endereço de e-mail de um cliente existente. Em vez de chamar `table_operation::insert_entity`, esse código usa `table_operation::replace_entity`. Essa abordagem faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada. Se ele tiver sido alterado, a operação falhará. Essa falha impede que o aplicativo substitua uma alteração feita entre a recuperação e a atualização por outro componente. O tratamento correto dessa falha é recuperar a entidade novamente, fazer suas alterações, se ainda for válida, e realizar outra operação de `table_operation::replace_entity`.  

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

`table_operation::replace_entity` operações falharão se a entidade tiver sido alterada desde que foi recuperada do servidor. Além disso, você deve recuperar a entidade do servidor primeiro para que o `table_operation::replace_entity` seja bem-sucedido. Às vezes, você não sabe se a entidade existe no servidor. Os valores atuais armazenados nele são irrelevantes, pois a atualização deve substituir todos eles. Para realizar esse resultado, use uma operação `table_operation::insert_or_replace_entity`. Esta operação insere a entidade se ela não existir. A operação substituirá a entidade se ela existir. No exemplo de código a seguir, a entidade Customer para `Jeff Smith` ainda é recuperada, mas é salva novamente no servidor usando `table_operation::insert_or_replace_entity`. Todas as atualizações efetuadas à entidade entre as operações de obtenção e atualização serão substituídas.  

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

Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. A consulta no código a seguir usa o método `table_query::set_select_columns` para retornar apenas os endereços de email das entidades na tabela.  

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

## <a name="delete-content"></a>Excluir conteúdo

### <a name="delete-an-entity"></a>Eliminar uma entidade

Você pode excluir uma entidade depois de recuperá-la. Depois de recuperar uma entidade, chame `table_operation::delete_entity` com a entidade a ser excluída. Em seguida, chame o método `cloud_table.execute`. O código a seguir recupera e exclui uma entidade com uma chave de partição de `Smith` e uma chave de linha de `Jeff`.

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

Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Uma tabela que foi excluída não está disponível para ser recriada por algum tempo após a exclusão.  

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

## <a name="troubleshooting"></a>Resolução de problemas

Para o Visual Studio Community Edition, se seu projeto obtiver erros de compilação devido aos arquivos de inclusão *storage_account. h* e *Table. h*, remova a opção de compilador **/permissive-** :

1. No **Explorador de Soluções**, clique com o botão direito do rato no seu projeto e selecione **Propriedades**.
1. Na caixa de diálogo **Páginas de Propriedades**, expanda **Propriedades de Configuração**, expanda **C/C++** e selecione **Idioma**.
1. Definir **Modo de conformidade** para **Não**.

## <a name="next-steps"></a>Passos seguintes

O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

Siga estas ligações para saber mais sobre o Armazenamento do Microsoft Azure e a API de tabela do Azure Cosmos DB:

* [Introdução à API da Tabela](table-introduction.md)
* [Listar Recursos do Armazenamento do Microsoft Azure em C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referência da Biblioteca de Cliente do Armazenamento para C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Microsoft Azure](https://azure.microsoft.com/documentation/services/storage/)
