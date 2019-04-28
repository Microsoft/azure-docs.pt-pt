---
title: Como utilizar o armazenamento de tabelas do Azure e a API de tabela do Azure Cosmos DB com C++
description: Armazene dados estruturados na cloud com o armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 40b84a56f93ad670a26eb876a18820e0d4037f63
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130556"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Como utilizar o Armazenamento de Tabelas do Azure e a API de Tabela do Azure Cosmos DB com C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Descrição geral
Este guia vai mostrar como realizar cenários comuns com a API de serviço de armazenamento de Tabelas do Azure ou de Tabela do Azure Cosmos DB. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **criar e eliminar uma tabela** e **trabalhar com entidades de tabela**.

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A versão recomendada é Storage Client Library 2.2.0, que está disponível através de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, irá utilizar as funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação de C++. Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.  

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções tendo em conta sobre o [biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.  
* **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de Pacotes NuGet >Consola do Gestor de Pacotes**. Escreva o seguinte comando na [consola do Gestor de pacotes NuGet](/nuget/tools/package-manager-console) e prima Enter.  
  
     Pacote de instalação wastorage

## <a name="configure-access-to-the-table-client-library"></a>Configurar o acesso à biblioteca de cliente de tabela
Adicione as seguintes declarações na parte superior do ficheiro C++ em que pretende utilizar as APIs de armazenamento do Azure para aceder a tabelas:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Um cliente do Armazenamento do Azure ou cliente de do Cosmos DB utiliza uma cadeia de ligação para armazenar pontos finais e credenciais para aceder aos serviços de gestão de dados. Quando executar uma aplicação de cliente, tem de fornecer a cadeia de ligação de armazenamento ou a cadeia de ligação de base de dados do Azure Cosmos DB no formato adequado.

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Armazenamento do Microsoft Azure
 Utilize o nome da sua conta de armazenamento e a chave de acesso da conta do Storage listada no [Portal do Azure](https://portal.azure.com) para os valores de *AccountName* e *AccountKey*. Para obter mais informações sobre contas de armazenamento, veja [Acerca das contas de Armazenamento do Microsoft Azure](../storage/common/storage-create-storage-account.md). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação do Armazenamento do Microsoft Azure:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Configurar uma cadeia de ligação Azure Cosmos DB
Utilize o nome da sua conta de base de dados do Azure Cosmos DB, a chave primária e o ponto final listado no [Portal do Azure](https://portal.azure.com) para os valores de *nome da conta*, *chave primária*, e *ponto final*. Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação do Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Para testar a aplicação no seu computador local baseado no Windows, pode utilizar o [emulador de armazenamento do Azure](../storage/common/storage-use-emulator.md) que é instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços tabela, fila e blob do Azure disponíveis no computador de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento do Azure, clique no botão **Iniciar** ou prima a tecla do logótipo do Windows. Comece a escrever **Emulador de armazenamento do Microsoft Azure**e, em seguida, selecione **Emulador de armazenamento do Microsoft Azure** da lista de aplicações.  

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Obter a cadeia de ligação
Pode utilizar a classe **cloud_storage_account** para representar as suas informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma classe **cloud_table_client**, uma vez que lhe permite obter objetos de referência para as tabelas e entidades armazenadas no âmbito do serviço de Armazenamento de Tabelas. O código seguinte cria um objeto **cloud_table_client**, utilizando o objeto de conta de armazenamento que obtivemos acima:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Criar uma tabela
Um objeto **cloud_table_client** permite-lhe obter objetos de referência para as tabelas e entidades. O código seguinte cria um objeto **cloud_table_client** e utiliza-o para criar uma nova tabela.

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

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo objeto **table_entity** e transmita-o para **table_operation::insert_entity**. O código seguinte utiliza o nome próprio do cliente como a chave da fila e o apelido como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam de forma exclusiva a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as entidades com chaves de partição diferentes, mas a utilização de várias chaves de partição permite uma maior escalabilidade de operações simultâneas. Para obter mais informações, consulte a [Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure](../storage/common/storage-performance-checklist.md).

O código seguinte cria uma nova instância do **table_entity** com alguns dados de cliente para armazenar. O código seguinte chama **table_operation::insert_entity** para criar um objeto de **table_operation** a inserir uma entidade numa tabela e associa a nova entidade com a tabela. Por fim, o código chama o método executar no objeto **cloud_table**. E o novo **table_operation** envia um pedido para o serviço Tabela para inserir a entidade de cliente novo na tabela "pessoas".  

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

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir um lote de entidades no serviço Tabela numa operação de escrita. O código seguinte cria um objeto de **table_batch_operation** e, em seguida, adiciona três operações de inserção ao mesmo. Cada operação de inserção é adicionada ao criar um novo objeto de entidade, definindo os respetivos valores e, em seguida, ao chamar o método de inserção no objeto **table_batch_operation** para associar a entidade com uma nova operação de inserção. Em seguida, chama-se o **cloud_table.execute** para executar a operação.  

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

* Pode efetuar até 100 operações de inserir, eliminar, unir, substituir, inserir ou intercalar, e inserir ou substituir em qualquer combinação num lote único.  
* As operações em lote podem ter uma operação de obter se for a única operação no lote.  
* Todas as entidades numa única operação em lote têm de ter a mesma chave de partição.  
* Uma operação em lote está limitada a um payload de dados de 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para consultar uma tabela para todas as entidades numa partição, utilize um objeto **table_query**. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.  

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

A consulta neste exemplo inclui todas as entidades que correspondem aos critérios de filtro. Se tem tabelas grandes e tem de transferir as entidades de tabela muitas vezes, recomendamos que armazene os dados em blobs de armazenamento do Azure em vez disso.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades numa partição
Se não pretender consultar todas as entidades de uma partição, pode especificar um intervalo através da combinação do filtro da chave de partição com um filtro da chave da fila. O exemplo de código seguinte utiliza dois filtros para obter todas as entidades na partição “Santos”, em que a chave da fila (nome próprio) começa com uma letra anterior ao “E” do alfabeto e, em seguida, imprime os resultados da consulta.  

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

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza **table_operation::retrieve_entity** para especificar o cliente "Jorge Santos". Este método devolve apenas uma entidade em vez de uma coleção, e o valor devolvido em **table_result**. Especificar as chaves de partição e da fila numa consulta é a forma mais rápida de obter uma única entidade a partir do serviço Tabela.  

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

## <a name="replace-an-entity"></a>Substituir uma entidade
Para substituir uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, guarde as alterações novamente no serviço Tabela. O código seguinte altera o número de telefone e o endereço de e-mail de um cliente existente. Em vez de chamar **table_operation::insert_entity**, este código utiliza **table_operation::replace_entity**. Isto faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi obtida, caso em que a operação falhará. Esta falha é para impedir que a sua aplicação substitua inadvertidamente uma alteração efetuada entre a obtenção e a atualização por outro componente da aplicação. O processamento adequado desta falha é para obter a entidade novamente, efetuar as alterações (se ainda forem válidas) e, em seguida, executar outra operação de **table_operation::replace_entity**. A secção seguinte irá mostrar como substituir este comportamento.  

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

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade
As operações de **table_operation::replace_entity** irão falhar se a entidade tiver sido alterada desde que foi obtida do servidor. Além disso, tem de obter primeiro a entidade do servidor para que a operação de **table_operation::replace_entity** seja concluída com êxito. Por vezes, no entanto, não sabe se a entidade existe no servidor e os valores atuais armazenados na mesma são irrelevantes — a sua atualização deve substituí-los todos. Para tal, utilizaria uma operação **table_operation::insert_or_replace_entity**. Esta operação insere a entidade se esta não existir ou substitui-a se existir, independentemente de quando foi efetuada a última atualização. No seguinte exemplo de código, a entidade de cliente para Jorge Santos ainda é obtida, mas, em seguida, é guardada no servidor através de **table_operation::insert_or_replace_entity**. Todas as atualizações efetuadas à entidade entre as operações de obtenção e atualização serão substituídas.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>Consultar um subconjunto de propriedades de entidade
Uma consulta a uma tabela pode obter apenas algumas propriedades de uma entidade. A consulta no seguinte código utiliza o método **table_query::set_select_columns** para devolver apenas os endereços de e-mail de entidades na tabela.  

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
> 

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode facilmente eliminar uma entidade depois de a ter obtido. Assim que a entidade é obtida, chamar **table_operation::delete_entity** com a entidade a eliminar. Em seguida, chame o método **cloud_table.execute**. O código seguinte obtém e elimina uma entidade com uma chave de partição de "Santos" e uma chave de linha de "Jorge".  

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

## <a name="delete-a-table"></a>Eliminar uma tabela
Finalmente, o seguinte exemplo de código elimina uma tabela a partir de uma conta do Storage. Uma tabela que foi eliminada estará indisponível para ser recriada durante um determinado período de tempo após a eliminação.  

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
* Erros de compilação no Visual Studio 2017 Community Edition

  Se o seu projeto obtém erros de compilação devido a incluir ficheiros storage_account.h e table.h, remova o interruptor **/ permissivo-** compilador. 
  - No **Explorador de Soluções**, clique com o botão direito do rato no seu projeto e selecione **Propriedades**.
  - Na caixa de diálogo **Páginas de Propriedades**, expanda **Propriedades de Configuração**, expanda **C/C++** e selecione **Idioma**.
  - Definir **Modo de conformidade** para **Não**.
   
## <a name="next-steps"></a>Passos Seguintes
Siga estas ligações para saber mais sobre o Armazenamento do Microsoft Azure e a API de tabela do Azure Cosmos DB: 

* [Introdução à API da Tabela](table-introduction.md)
* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [Listar Recursos do Armazenamento do Microsoft Azure em C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Referência da Biblioteca de Cliente do Armazenamento para C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Microsoft Azure](https://azure.microsoft.com/documentation/services/storage/)
