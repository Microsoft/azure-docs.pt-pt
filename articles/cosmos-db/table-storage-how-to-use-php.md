---
title: Como utilizar o serviço Tabela de Armazenamento do Azure ou a API de Tabelas do Azure Cosmos DB a partir de PHP
description: Armazene dados estruturados na cloud com o armazenamento de Tabelas do Azure ou a API de Tabelas do Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: aac6755ed90c795b8fff09d9ffde33878ad21a32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111502"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Como utilizar o serviço Tabela de Armazenamento do Azure ou a API de Tabelas do Azure Cosmos DB a partir de PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como realizar cenários comuns com o serviço Tabela de Armazenamento do Azure e a API de Tabelas do Azure Cosmos DB. Os exemplos são escritos em PHP e utilizam a [Biblioteca de Cliente PHP de Tabela de Armazenamento do Azure ][download]. Os cenários abrangidos incluem **criar e eliminar uma tabela** e **inserir, eliminar e consultar entidades numa tabela**. Para obter mais informações sobre o serviço Tabela do Azure, consulte a secção [Passos seguintes](#next-steps).


## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Criar uma conta do Storage do Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação PHP

O único requisito para criar uma aplicação PHP para aceder ao serviço Tabela de Armazenamento ou à API de Tabelas do Azure Cosmos DB é referenciar classes no SDK azure-storage-table para PHP a partir do seu código. Pode utilizar qualquer ferramenta de desenvolvimento para criar a sua aplicação, incluindo o Notepad.

Neste guia, utilizará as funcionalidades do serviço Tabela de Armazenamento ou do Azure Cosmos DB que podem ser chamadas a partir de uma aplicação PHP localmente ou em código executado numa função da Web, função de trabalho ou site do Azure.

## <a name="get-the-client-library"></a>Obter a biblioteca de cliente

1. Crie um ficheiro chamado composer.json no diretório raiz do seu projeto e adicione o seguinte código ao mesmo:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Transfira o ficheiro [composer.phar](https://getcomposer.org/composer.phar) no seu diretório raiz. 
3. Abra uma linha de comandos e execute o seguinte comando no diretório raiz do seu projeto:
   ```
   php composer.phar install
   ```
   Em alternativa, aceda à [Biblioteca de Cliente PHP de Tabela de Armazenamento do Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) no GitHub para clonar o código fonte.


## <a name="add-required-references"></a>Adicionar as referências necessárias
Para utilizar o serviço Tabela de Armazenamento ou as APIs do Azure Cosmos DB, tem de:

* Referenciar o ficheiro do carregador automático com a instrução [require_once][require_once].
* Referenciar todas as classes que utilizar.

O seguinte exemplo mostra como incluir o ficheiro do carregador automático e referenciar a classe **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Nos exemplos abaixo, a instrução `require_once` é sempre apresentada, mas só são referenciadas as classes necessárias para executar o exemplo.

## <a name="add-a-storage-table-service-connection"></a>Adicionar uma ligação do serviço Tabela de Armazenamento
Para instanciar um cliente do serviço Tabela de Armazenamento, primeiro tem de ter uma cadeia de ligação válida. O formato da cadeia de ligação do serviço Tabela de Armazenamento é o seguinte:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Adicionar uma ligação do Azure Cosmos DB
Para instanciar um cliente de Tabelas do Azure Cosmos DB, primeiro tem de ter uma cadeia de ligação válida. O formato da cadeia de ligação do Azure Cosmos DB é:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Adicionar uma ligação do emulador de Armazenamento
Para aceder ao emulador de armazenamento:

```php
UseDevelopmentStorage = true
```

Para criar um cliente do serviço Tabela de Armazenamento ou do Azure Cosmos DB, precisa de utilizar a classe **TableRestProxy**. Pode:

* Transmitir a cadeia de ligação diretamente para o mesmo ou
* Utilizar a classe **CloudConfigurationManager (CCM)** para verificar múltiplas origens de dados externas para a cadeia de ligação:
  * Por predefinição, esta vem com suporte para uma origem de dados externa – variáveis de ambiente.
  * Pode adicionar novas origem de dados ao expandir a classe `ConnectionStringSource`.

Nos exemplos aqui descritos, a cadeia de ligação é transmitida diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Criar uma tabela
Um objeto **TableRestProxy** permite-lhe criar uma tabela com o método **createTable**. Ao criar uma tabela, pode definir o tempo limite do serviço Tabela. (Para obter mais informações sobre o tempo limite do serviço Tabela, veja [Setting Timeouts for Table Service Operations][table-service-timeouts] [Definir Tempos Limite para Operações do Serviço Tabela].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Para obter mais informações sobre as restrições em nomes de tabelas, veja [Understanding the Table Service Data Model][table-data-model] (Compreender o Modelo de Dados do Serviço Tabela).

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo objeto de **Entity** e transmita-o para **TableRestProxy->insertEntity**. Tenha em atenção que, quando cria uma entidade, tem de especificar uma `PartitionKey` e uma `RowKey`. Estes são os identificadores exclusivos de uma entidade e os valores que podem ser consultados muito mais rapidamente do que as outras propriedades da entidade. O sistema utiliza a `PartitionKey` para distribuir automaticamente as entidades da tabela através de vários nós de Armazenamento. As entidades com a mesma `PartitionKey` são armazenadas no mesmo nó. (As operações em múltiplas entidades armazenadas no mesmo nó têm um desempenho melhor do que em entidades armazenadas em diferentes nós.) A `RowKey` é o ID exclusivo de uma entidade numa partição.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Para obter informações sobre os tipos e propriedades da Tabela, veja [Understanding the Table Service Data Model][table-data-model] (Noções Básicas sobre o Modelo de Dados do Serviço Tabela).

A classe **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para utilizar estes métodos, crie uma nova **Entidade** e transfira-a como um parâmetro para um dos métodos. Cada método será inserido na entidade se não existir. Se a entidade já existir, o método **insertOrMergeEntity** atualizará os valores de propriedade se as propriedades já existirem e adicionará novas propriedades se estas não existirem, enquanto o método **insertOrReplaceEntity** substitui totalmente uma entidade existente. O seguinte exemplo mostra como utilizar o método **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, será inserida. No entanto, se tiver sido inserida anteriormente (como apresentado no exemplo acima), a propriedade `DueDate` será atualizada e a propriedade `Status` será adicionada. As propriedades `Description` e `Location` também são atualizadas, mas com valores que as deixam efetivamente inalteradas. Se estas últimas duas propriedades não tiverem sido adicionadas conforme apresentado no exemplo, mas existirem na entidade de destino, os respetivos valores existentes permanecerão inalterados.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
O método **TableRestProxy->getEntity** permite-lhe obter uma única entidade ao consultar as respetivas `PartitionKey` e `RowKey`. No exemplo abaixo, a chave de partição `tasksSeattle` e a chave de linha `1` são transferidas para o método **getEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
As consultas de entidades são criadas com filtros (para obter mais informações, veja [Querying Tables and Entities][filters] [Consultar Tabelas e Entidades]). Para obter todas as entidades na partição, utilize o filtro "PartitionKey eq *nome_da_partição*". O seguinte exemplo mostra como obter todas as entidades na partição `tasksSeattle` ao transmitir um filtro para o método **queryEntities**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Obter um subconjunto de entidades numa partição
O mesmo padrão utilizado no exemplo anterior pode ser utilizado para obter qualquer subconjunto de entidades numa partição. O subconjunto de entidades que obtém é determinado pelo filtro que utiliza (para obter mais informações, veja [Querying Tables and Entities][filters] [Consultar Tabelas e Entidades]). O seguinte exemplo mostra como utilizar um filtro para obter todas as entidades com uma `Location` específica e uma `DueDate` anterior a uma data específica.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Obter um subconjunto de propriedades de entidade
Uma consulta pode obter um subconjunto de propriedades de entidade. Esta técnica, denominada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades grandes. Para especificar uma propriedade a obter, transfira o nome da propriedade para o método **Query->addSelectField**. Pode chamar este método várias vezes para adicionar mais propriedades. Após executar o método **TableRestProxy->queryEntities**, as entidades devolvidas terão apenas as propriedades selecionadas. (Se quiser devolver um subconjunto de entidades de Tabela, utilize um filtro conforme apresentado nas consultas acima.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Atualizar uma entidade
Pode atualizar uma entidade existente ao utilizar os métodos **Entity->setProperty** e **Entity->addProperty** na entidade e, em seguida, chamar o método **TableRestProxy->updateEntity**. O seguinte exemplo obtém uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Tenha em atenção que pode remover uma propriedade ao definir o respetivo valor para **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Eliminar uma entidade
Para eliminar uma entidade, transfira o nome da tabela e as chaves `PartitionKey` e `RowKey` para o método **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para as verificações de simultaneidade, pode definir a ETag para que uma entidade seja eliminada ao utilizar o método **DeleteEntityOptions->setEtag** e transmitir o objeto **DeleteEntityOptions** para o método **deleteEntity** como quarto parâmetro.

## <a name="batch-table-operations"></a>Operações de tabela de lote
O método **TableRestProxy->batch** permite-lhe executar múltiplas operações num único pedido. Este padrão envolve a adição de operações ao objeto **BatchRequest** e, em seguida, a transmissão do objeto **BatchRequest** para o método **TableRestProxy->batch**. Para adicionar uma operação a um objeto **BatchRequest**, pode chamar qualquer um dos seguintes métodos múltiplas vezes:

* **addInsertEntity** (adiciona uma operação insertEntity)
* **addUpdateEntity** (adiciona uma operação updateEntity)
* **addMergeEntity** (adiciona uma operação mergeEntity)
* **addInsertOrReplaceEntity** (adiciona uma operação insertOrReplaceEntity)
* **addInsertOrMergeEntity** (adiciona uma operação insertOrMergeEntity)
* **addDeleteEntity** (adiciona uma operação deleteEntity)

O seguinte exemplo mostra como executar as operações **insertEntity** e **deleteEntity** num único pedido. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Para obter mais informações sobre as operações de Tabela de lote, veja [Performing Entity Group Transactions][entity-group-transactions] (Efetuar Transações de Grupo de Entidades).

## <a name="delete-a-table"></a>Eliminar uma tabela
Por fim, para eliminar uma tabela, transmita o nome da tabela para o método **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passos Seguintes
Agora que já tem as noções básicas sobre o serviço Tabela do Azure e o Azure Cosmos DB, siga estas ligações para saber mais.

* O [Explorador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

* [Centro de Programadores de PHP](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
