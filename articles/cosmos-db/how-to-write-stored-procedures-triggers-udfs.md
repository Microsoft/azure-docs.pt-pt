---
title: Como escrever procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB
description: Saiba como definir os procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: b0c09c5b425beef6badff7fb6ec298f96591abc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57990543"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como escrever procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB

O Azure Cosmos DB oferece a execução transacional, integrada à linguagem do JavaScript que permite que escreva **procedimentos armazenados**, **acionadores**, e **funções definidas pelo utilizador (UDFs)**. Ao utilizar a API de SQL no Azure Cosmos DB, pode definir os procedimentos armazenados, acionadores e UDFs na linguagem JavaScript. Pode escrever a lógica em JavaScript e executá-lo dentro do motor de base de dados. Pode criar e executar UDFs, procedimentos armazenados e acionadores usando [portal do Azure](https://portal.azure.com/), o [linguagem JavaScript integrada a API de consulta no Azure Cosmos DB](javascript-query-api.md) e o [cliente Cosmos DB SQL API SDKs](sql-api-dotnet-samples.md). 

Para chamar um procedimento armazenado, um acionador e uma função definida pelo utilizador, é preciso registrá-la. Para obter mais informações, consulte [como trabalhar com procedimentos armazenados, disparadores, funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

## <a id="stored-procedures"></a>Como escrever procedimentos armazenados

Procedimentos armazenados são escritos usando JavaScript, podem criar, atualizar, ler, consultar e eliminar itens dentro de um contentor do Cosmos do Azure. Procedimentos armazenados estão registados por coleção e podem operar em qualquer documento ou de um anexo presente nessa coleção.

**Exemplo**

Aqui está um simple procedimento armazenado que retorna uma resposta de "Hello World".

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

O objeto context fornece acesso a todas as operações que podem ser executadas no Azure Cosmos DB, bem como acesso a objetos de solicitação e resposta. Neste caso, usar o objeto de resposta para definir o corpo da resposta a serem enviados ao cliente.

Uma vez escrito, o procedimento armazenado tem de estar registado com uma coleção. Para obter mais informações, consulte [como utilizar os procedimentos armazenados no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) artigo.

### <a id="create-an-item"></a>Criar um item a utilizar o procedimento armazenado

Quando cria um item ao utilizar o procedimento armazenado, o item é inserido o contentor do Azure Cosmos DB e um id para o item criado recentemente, que ele retornado. Criar um item é uma operação assíncrona e depende das funções de retorno de chamada de JavaScript. A função de retorno de chamada tem dois parâmetros: um para o objeto de erro no caso da que operação falhar e outro para um valor de retorno; Neste caso, o objeto criado. Dentro do retorno de chamada, pode tratar a exceção ou lançar um erro. No caso de um retorno de chamada não for fornecido, e existe um erro, o tempo de execução do Azure Cosmos DB irá gerar um erro. 

O procedimento armazenado também inclui um parâmetro para definir a descrição, é um valor booleano. Quando o parâmetro é definido como true e a descrição está em falta, o procedimento armazenado lançará uma exceção. Caso contrário, o restante do procedimento armazenado continua a ser executado.

O seguinte procedimento armazenado de exemplo usa um novo item do Azure Cosmos DB como entrada, ele insere o contentor do Azure Cosmos DB e retorna a id do item criado recentemente. Neste exemplo, podemos tirar partido das exemplo ToDoList do [API de SQL de .NET de início rápido](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Matrizes como parâmetros de entrada para procedimentos armazenados 

Ao definir um procedimento armazenado no portal do Azure, os parâmetros de entrada são sempre enviados como uma cadeia de caracteres para o procedimento armazenado. Mesmo se passar uma matriz de cadeias de caracteres como entrada, a matriz é convertida em cadeia de caracteres e enviada para o procedimento armazenado. Para contornar este problema, pode definir uma função dentro de seu procedimento armazenado para analisar a cadeia de caracteres como uma matriz. O código seguinte mostra como um parâmetro de entrada de cadeia de caracteres como uma matriz de analisar:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Transações em procedimentos armazenados

Pode implementar transações nos itens dentro de um contêiner usando um procedimento armazenado. O exemplo seguinte utiliza transações dentro de uma aplicação de jogos de futebol de fantasia para jogadores trade entre duas equipes numa única operação. O procedimento armazenado tenta ler os dois itens de Azure Cosmos DB, cada um correspondendo aos IDs de player passado como um argumento. Se os dois jogadores forem encontrados, o procedimento armazenado atualiza os itens ao trocar as suas equipas. Se for encontrado qualquer erro ao longo do caminho, o procedimento armazenado lança uma exceção de JavaScript que implicitamente anulá-la.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>Execução vinculada em procedimentos armazenados

Segue-se um exemplo de um procedimento armazenado que em massa-imports itens para um contentor do Cosmos do Azure. O procedimento armazenado lida com execução vinculada ao verificar o valor de retorno booleano `createDocument`e, em seguida, usa a contagem de itens introduzidos em cada invocação do procedimento armazenado para controlar e retomar o progresso em lotes.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>Como escrever acionadores

Azure Cosmos DB suporta pré- acionadores e pós-acionadores. Pré-acionadores são executados antes de modificar um item de base de dados e pós-acionadores são executados depois de modificar um item de base de dados.

### <a id="pre-triggers"></a>Pré-acionadores

O exemplo seguinte mostra como um acionador de pré-lançamento é usado para validar as propriedades de um item do Azure Cosmos DB que está a ser criada. Neste exemplo, podemos estão utilizando o exemplo ToDoList do [API de SQL de .NET de início rápido](create-sql-api-dotnet.md), para adicionar uma propriedade de timestamp a um item adicionado recentemente que tenha um.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Pré-acionadores não podem ter parâmetros de entrada. O objeto de solicitação no acionador é usado para manipular a mensagem de solicitação associada com a operação. No exemplo anterior, o Pre-acionador é executado durante a criação de um item do Azure Cosmos DB e o corpo da mensagem de pedido contém o item a ser criada no formato JSON.

Quando os acionadores são registrados, pode especificar as operações que pode ser executado com. Este acionador deve ser criado com uma `TriggerOperation` valor de `TriggerOperation.Create`, que significa que não é permitido com o acionador numa operação de substituição, conforme mostrado no código a seguir.

Para obter exemplos de como registar e chamar um acionador de pré-lançamento, consulte [pré-acionadores](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-acionadores](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) artigos. 

### <a id="post-triggers"></a>Pós-acionadores

O exemplo seguinte mostra um pós acionador de. Este acionador consulta para o item de metadados e atualiza-o com detalhes sobre o item criado recentemente.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Uma coisa que é importante observar é a execução transacional de acionadores no Azure Cosmos DB. Este pós-acionador é executado como parte da mesma transação que é utilizada pelo Azure Cosmos DB create item. Por conseguinte, se ocorrer uma exceção durante a execução do pós-acionador de, por exemplo, se for não é possível atualizar o item de metadados, a transação inteira falhará e ela é revertida. Portanto, o item do Azure Cosmos DB é criado e uma exceção será retornada.

Para obter exemplos de como registar e chamar um acionador de pré-lançamento, consulte [pré-acionadores](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-acionadores](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) artigos. 

## <a id="udfs"></a>Como escrever funções definidas pelo utilizador

O exemplo seguinte cria uma UDF para calcular o imposto de renda para vários colchetes de renda. Esta função definida pelo utilizador, em seguida, seria usada dentro de uma consulta. Para efeitos deste exemplo supor que existe um recipiente chamado "Incomes" com as propriedades da seguinte forma:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Segue-se uma definição de função para calcular o imposto de renda para vários colchetes de renda:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Para obter exemplos de como registar e utilizar uma função definida pelo utilizador, consulte [como utilizar funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) artigo.

## <a name="next-steps"></a>Passos Seguintes

Saber mais, conceitos e procedimento escrita ou usar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB:

* [Como registar e utilizar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como escrever procedimentos armazenados e acionadores com a API de consulta do Javascript no Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Trabalhar com o Azure Cosmos DB procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Trabalhar com a linguagem JavaScript integrada a API de consulta no Azure Cosmos DB](javascript-query-api.md)
