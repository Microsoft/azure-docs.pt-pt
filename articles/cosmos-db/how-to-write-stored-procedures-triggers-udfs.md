---
title: Gravar procedimentos armazenados, gatilhos e UDFs no Azure Cosmos DB
description: Saiba como definir procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4dee017323bda5fc08598a9b24cadd11516807cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441722"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB

O Azure Cosmos DB fornece execução transacional e integrada de linguagem de JavaScript que permite escrever **procedimentos armazenados**, **gatilhos**e **UDFs (funções definidas pelo usuário)** . Ao usar a API do SQL no Azure Cosmos DB, você pode definir os procedimentos armazenados, gatilhos e UDFs na linguagem JavaScript. Você pode escrever sua lógica em JavaScript e executá-la dentro do mecanismo de banco de dados. Você pode criar e executar gatilhos, procedimentos armazenados e UDFs usando [portal do Azure](https://portal.azure.com/), a [API de consulta integrada à linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md) e os [SDKs do cliente de API do SQL Cosmos DB](sql-api-dotnet-samples.md). 

Para chamar um procedimento armazenado, um gatilho e uma função definida pelo usuário, você precisa registrá-lo. Para obter mais informações, consulte [como trabalhar com procedimentos armazenados, gatilhos, funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Para contêineres particionados, ao executar um procedimento armazenado, um valor de chave de partição deve ser fornecido nas opções de solicitação. Os procedimentos armazenados sempre estão no escopo de uma chave de partição. Os itens que têm um valor de chave de partição diferente não serão visíveis para o procedimento armazenado. Isso também se aplica a gatilhos.

> [!Tip]
> O cosmos dá suporte à implantação de contêineres com procedimentos armazenados, gatilhos e funções definidas pelo usuário. Para obter mais informações, consulte [criar um contêiner de Azure Cosmos DB com a funcionalidade do lado do servidor.](manage-sql-with-resource-manager.md#create-sproc)

## <a id="stored-procedures"></a>Como escrever procedimentos armazenados

Os procedimentos armazenados são escritos usando JavaScript, eles podem criar, atualizar, ler, consultar e excluir itens dentro de um contêiner Cosmos do Azure. Os procedimentos armazenados são registrados por coleção e podem operar em qualquer documento ou anexo presente nessa coleção.

**Exemplo**

Aqui está um procedimento armazenado simples que retorna uma resposta "Olá, Mundo".

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

O objeto de contexto fornece acesso a todas as operações que podem ser executadas em Azure Cosmos DB, bem como acesso aos objetos de solicitação e resposta. Nesse caso, você usa o objeto de resposta para definir o corpo da resposta a ser enviada de volta ao cliente.

Uma vez gravado, o procedimento armazenado deve ser registrado com uma coleção. Para saber mais, consulte [como usar procedimentos armazenados no artigo Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) .

### <a id="create-an-item"></a>Criar um item usando o procedimento armazenado

Quando você cria um item usando o procedimento armazenado, o item é inserido no contêiner Cosmos do Azure e uma ID para o item recém-criado é retornada. Criar um item é uma operação assíncrona e depende das funções de retorno de chamada do JavaScript. A função de retorno de chamada tem dois parâmetros: um para o objeto de erro em caso de falha da operação e outro para um valor de retorno; Nesse caso, o objeto criado. Dentro do retorno de chamada, você pode manipular a exceção ou gerar um erro. Caso um retorno de chamada não seja fornecido e haja um erro, o tempo de execução do Azure Cosmos DB gerará um erro. 

O procedimento armazenado também inclui um parâmetro para definir a descrição, é um valor booliano. Quando o parâmetro for definido como true e a descrição estiver ausente, o procedimento armazenado gerará uma exceção. Caso contrário, o restante do procedimento armazenado continuará a ser executado.

O procedimento armazenado de exemplo a seguir usa um novo item Cosmos do Azure como entrada, insere-o no contêiner Cosmos do Azure e retorna a ID para o item recém-criado. Neste exemplo, estamos aproveitando o exemplo ToDolist da [API SQL do início rápido do .net](create-sql-api-dotnet.md)

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

Ao definir um procedimento armazenado no portal do Azure, os parâmetros de entrada são sempre enviados como uma cadeia de caracteres para o procedimento armazenado. Mesmo se passar uma matriz de cadeias de caracteres como entrada, a matriz é convertida em cadeia de caracteres e enviada para o procedimento armazenado. Para solucionar esse erro, você pode definir uma função em seu procedimento armazenado para analisar a cadeia de caracteres como uma matriz. O código a seguir mostra como analisar um parâmetro de entrada de cadeia de caracteres como uma matriz:

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

Você pode implementar transações em itens dentro de um contêiner usando um procedimento armazenado. O exemplo a seguir usa transações em um aplicativo de jogos de futebol de fantasia para trocar jogadores entre duas equipes em uma única operação. O procedimento armazenado tenta ler os dois itens de Cosmos do Azure, cada um correspondendo às IDs de Player passadas como um argumento. Se ambos os players forem encontrados, o procedimento armazenado atualizará os itens permutando suas equipes. Se algum erro for encontrado ao longo do caminho, o procedimento armazenado lançará uma exceção JavaScript que anula implicitamente a transação.

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

### <a id="bounded-execution"></a>Execução limitada em procedimentos armazenados

Veja a seguir um exemplo de um procedimento armazenado que importa os itens em massa para um contêiner Cosmos do Azure. O procedimento armazenado lida com a execução limitada, verificando o valor de retorno booliano de `createDocument`e, em seguida, usa a contagem de itens inseridos em cada invocação do procedimento armazenado para rastrear e retomar o progresso entre os lotes.

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

## <a id="triggers"></a>Como escrever gatilhos

O Azure Cosmos DB dá suporte a pré-gatilhos e pós-gatilhos. Os pré-gatilhos são executados antes de modificar um item de banco de dados e pós-gatilhos são executados após a modificação de um item de banco de dados.

### <a id="pre-triggers"></a>Pré-gatilhos

O exemplo a seguir mostra como um pré-gatilho é usado para validar as propriedades de um item Cosmos do Azure que está sendo criado. Neste exemplo, estamos aproveitando o exemplo ToDolist da [API SQL do início rápido do .net](create-sql-api-dotnet.md)para adicionar uma propriedade Timestamp a um item recém-adicionado, se ele não contiver um.

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

Os pré-acionadores não podem ter parâmetros de entrada. O objeto de solicitação no gatilho é usado para manipular a mensagem de solicitação associada à operação. No exemplo anterior, o pré-gatilho é executado ao criar um item Cosmos do Azure e o corpo da mensagem de solicitação contém o item a ser criado no formato JSON.

Quando os gatilhos são registrados, você pode especificar as operações com as quais ele pode ser executado. Esse gatilho deve ser criado com um valor `TriggerOperation` de `TriggerOperation.Create`, o que significa que usar o gatilho em uma operação de substituição, conforme mostrado no código a seguir, não é permitido.

Para obter exemplos de como registrar e chamar um pré-gatilho, consulte os artigos [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-disparadores](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) . 

### <a id="post-triggers"></a>Pós-gatilhos

O exemplo a seguir mostra um post-Trigger. Esse gatilho consulta o item de metadados e o atualiza com detalhes sobre o item recém-criado.


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

Uma coisa importante a ser observada é a execução transacional de gatilhos no Azure Cosmos DB. O post-Trigger é executado como parte da mesma transação para o item subjacente. Uma exceção durante a execução pós-gatilho falhará na transação inteira. Qualquer coisa confirmada será revertida e uma exceção será retornada.

Para obter exemplos de como registrar e chamar um pré-gatilho, consulte os artigos [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-disparadores](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) . 

## <a id="udfs"></a>Como gravar funções definidas pelo usuário

O exemplo a seguir cria um UDF para calcular o imposto de renda para vários colchetes de renda. Essa função definida pelo usuário seria então usada dentro de uma consulta. Para os fins deste exemplo, suponha que haja um contêiner chamado "invenha" com propriedades da seguinte maneira:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

A seguir está uma definição de função para calcular o imposto de renda para vários colchetes de renda:

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

Para obter exemplos de como registrar e usar uma função definida pelo usuário, consulte [como usar funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) artigo.

## <a name="logging"></a>Registo 

Ao usar procedimentos armazenados, gatilhos ou funções definidas pelo usuário, você pode registrar as etapas usando o comando `console.log()`. Este comando irá concentrar uma cadeia de caracteres para depuração quando `EnableScriptLogging` for definido como true, conforme mostrado no exemplo a seguir:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os conceitos e como escrever ou usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

* [Como registar e utilizar procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript no Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Trabalhando com Azure Cosmos DB procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Trabalhando com API de consulta integrada de linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md)
