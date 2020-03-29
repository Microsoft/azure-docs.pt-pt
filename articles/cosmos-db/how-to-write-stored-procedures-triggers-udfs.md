---
title: Escreva procedimentos armazenados, gatilhos e UDFs em Azure Cosmos DB
description: Saiba como definir procedimentos armazenados, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4dee017323bda5fc08598a9b24cadd11516807cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441722"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como escrever procedimentos, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB

O Azure Cosmos DB fornece uma execução transacional integrada em linguagem do JavaScript que permite escrever **procedimentos armazenados,** **gatilhos**e **funções definidas pelo utilizador (UDFs)**. Ao utilizar a API SQL em Azure Cosmos DB, pode definir os procedimentos armazenados, gatilhos e UDFs em linguagem JavaScript. Pode escrever a sua lógica no JavaScript e executá-la dentro do motor de base de dados. Pode criar e executar gatilhos, procedimentos armazenados e UDFs utilizando o [portal Azure,](https://portal.azure.com/)a Consulta Integrada de Consulta em [Língua JavaScript em Azure Cosmos DB](javascript-query-api.md) e o Cliente [API Cosmos DB SQL.](sql-api-dotnet-samples.md) 

Para chamar um procedimento armazenado, gatilho e função definida pelo utilizador, é necessário registá-lo. Para mais informações, consulte [Como trabalhar com procedimentos armazenados, gatilhos, funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Para os recipientes divididos, ao executar um procedimento armazenado, deve ser fornecido um valor-chave de partição nas opções de pedido. Os procedimentos armazenados são sempre remetos a uma chave de partição. Os itens que tenham um valor-chave de divisória diferente não serão visíveis ao procedimento armazenado. Isto também se aplica aos gatilhos.

> [!Tip]
> A Cosmos suporta a implantação de contentores com procedimentos armazenados, gatilhos e funções definidas pelo utilizador. Para mais informações consulte [Criar um recipiente Azure Cosmos DB com funcionalidade do lado do servidor.](manage-sql-with-resource-manager.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Como escrever procedimentos armazenados

Os procedimentos armazenados são escritos usando javaScript, podem criar, atualizar, ler, consultar e eliminar itens dentro de um recipiente Azure Cosmos. Os procedimentos armazenados são registados por recolha, e podem funcionar em qualquer documento ou anexo presente nessa coleção.

**Exemplo**

Aqui está um procedimento simples armazenado que devolve uma resposta "Hello World".

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

O objeto de contexto proporciona acesso a todas as operações que podem ser realizadas no Azure Cosmos DB, bem como acesso aos objetos de pedido e resposta. Neste caso, utiliza o objeto de resposta para definir o corpo da resposta a ser devolvido ao cliente.

Uma vez escrito, o procedimento armazenado deve ser registado com uma coleção. Para saber mais, consulte como utilizar procedimentos armazenados no artigo da [Azure Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Criar um item usando o procedimento armazenado

Quando se cria um item utilizando o procedimento armazenado, o item é inserido no recipiente Azure Cosmos e é devolvido um ID para o item recém-criado. Criar um item é uma operação assíncrona e depende das funções de callback JavaScript. A função de retorno tem dois parâmetros - um para o objeto de erro no caso de a operação falhar e outro por um valor de devolução; neste caso, o objeto criado. Dentro da chamada, pode lidar com a exceção ou lançar um erro. No caso de não ser fornecida uma chamada e houver um erro, o tempo de execução do Azure Cosmos DB irá lançar um erro. 

O procedimento armazenado também inclui um parâmetro para definir a descrição, é um valor booleano. Quando o parâmetro estiver definido e a descrição faltar, o procedimento armazenado lançará uma exceção. Caso contrário, o resto do procedimento armazenado continua a ser executado.

O procedimento armazenado a seguir toma um novo item Azure Cosmos como entrada, insere-o no recipiente Azure Cosmos e devolve o ID para o item recém-criado. Neste exemplo, estamos a aproveitar a amostra ToDoList da [Quickstart .NET SQL API](create-sql-api-dotnet.md)

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

Ao definir um procedimento armazenado no portal Azure, os parâmetros de entrada são sempre enviados como uma corda para o procedimento armazenado. Mesmo que passe uma série de cordas como entrada, a matriz é convertida em corda e enviada para o procedimento armazenado. Para contornar isto, pode definir uma função dentro do seu procedimento armazenado para analisar a corda como uma matriz. O seguinte código mostra como analisar um parâmetro de entrada de cadeia como uma matriz:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Transações dentro dos procedimentos armazenados

Pode implementar transações em itens dentro de um contentor utilizando um procedimento armazenado. O exemplo seguinte usa transações dentro de uma aplicação de jogos de fantasy football para trocar jogadores entre duas equipas numa única operação. O procedimento armazenado tenta ler os dois itens Azure Cosmos cada um correspondente aos IDs do jogador transmitidos como argumento. Se ambos os jogadores forem encontrados, o procedimento armazenado atualiza os itens trocando as suas equipas. Se houver erros ao longo do caminho, o procedimento armazenado lança uma exceção JavaScript que aborta implicitamente a transação.

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

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Execução delimitada dentro dos procedimentos armazenados

Segue-se um exemplo de um procedimento armazenado que importa a granel itens num contentor Azure Cosmos. O procedimento armazenado trata da execução delimitada, verificando o valor de devolução booleano de , e, em seguida, utiliza a contagem de itens inseridos em cada invocação do procedimento armazenado para rastrear e retomar o progresso através dos `createDocument`lotes.

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

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Como escrever gatilhos

A Azure Cosmos DB suporta pré-gatilhos e pós-gatilhos. Os pré-gatilhos são executados antes de modificar um item de base de dados e os pós-gatilhos são executados após modificar um item de base de dados.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Pré-acionadores

O exemplo que se segue mostra como um pré-gatilho é usado para validar as propriedades de um item Azure Cosmos que está a ser criado. Neste exemplo, estamos a aproveitar a amostra ToDoList da [Quickstart .NET SQL API,](create-sql-api-dotnet.md)para adicionar uma propriedade de timestamp a um item recém-adicionado se não contiver uma.

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

Os pré-acionadores não podem ter parâmetros de entrada. O objeto de pedido no gatilho é utilizado para manipular a mensagem de pedido associada à operação. No exemplo anterior, o pré-gatilho é executado ao criar um item Azure Cosmos, e o corpo de mensagem de pedido contém o item a ser criado em formato JSON.

Quando os gatilhos estiverem registados, pode especificar as operações com as quais pode ser executada. Este gatilho deve ser `TriggerOperation` criado `TriggerOperation.Create`com um valor de , o que significa que não é permitida a utilização do gatilho numa operação de substituição, tal como mostrado no código seguinte.

Por exemplo, como registar e ligar para um pré-gatilho, ver [artigos pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos.](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) 

### <a name="post-triggers"></a><a id="post-triggers"></a>Pós-acionadores

O exemplo que se segue mostra um pós-gatilho. Este gatilho consulta o item dos metadados e atualiza-o com detalhes sobre o item recém-criado.


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

Uma coisa que é importante notar é a execução transacional de gatilhos em Azure Cosmos DB. O pós-gatilho funciona como parte da mesma transação para o próprio item subjacente. Uma exceção durante a execução pós-desencadeamento falhará toda a transação. Qualquer coisa comprometida será retornada e uma exceção devolvida.

Por exemplo, como registar e ligar para um pré-gatilho, ver [artigos pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos.](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Como escrever funções definidas pelo utilizador

A amostra seguinte cria uma UDF para calcular o imposto sobre o rendimento para vários escalões de rendimento. Esta função definida pelo utilizador seria então utilizada dentro de uma consulta. Para efeitos deste exemplo, suponha que exista um contentor chamado "Rendas" com propriedades da seguinte forma:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Segue-se uma definição de função para calcular o imposto sobre o rendimento para vários escalões de rendimentos:

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

Por exemplo, como registar e utilizar uma função definida pelo utilizador, consulte [como utilizar funções definidas pelo utilizador no artigo da Azure Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md#udfs)

## <a name="logging"></a>Registo 

Ao utilizar o procedimento armazenado, os gatilhos ou as funções `console.log()` definidas pelo utilizador, pode registar os passos utilizando o comando. Este comando concentrará uma corda `EnableScriptLogging` para depuração quando for definido como mostrado no seguinte exemplo:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais conceitos e como escrever ou utilizar procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB:

* [Como registar e utilizar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como escrever procedimentos e gatilhos armazenados usando API Javascript Consulta ry em Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Trabalhar com a Azure Cosmos DB procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Trabalhar com a api integrada de consulta integrada em língua JavaScript em Azure Cosmos DB](javascript-query-api.md)
