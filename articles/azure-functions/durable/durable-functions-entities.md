---
title: Entidades duradouras - Funções Azure
description: Saiba o que são entidades duráveis e como usá-las na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632055"
---
# <a name="entity-functions"></a>Funções de entidade

As funções de entidade definem operações de leitura e atualização de pequenas peças de Estado, conhecidas como *entidades duráveis.* Tal como as funções de orquestrador, as funções da entidade são funções com um tipo especial de gatilho, o gatilho da *entidade*. Ao contrário das funções orquestradoras, as funções da entidade gerem explicitamente o estado de uma entidade, em vez de representarem implicitamente o Estado através do fluxo de controlo.
As entidades fornecem um meio para escalonar as aplicações distribuindo o trabalho por muitas entidades, cada uma com um estado modestamente dimensionado.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada só estão disponíveis em [Funções Duráveis 2.0](durable-functions-versions.md#migrate-from-1x-to-2x) ou superiores. Atualmente são suportados em .NET, JavaScript e Python.

## <a name="general-concepts"></a>Conceitos gerais

As entidades comportam-se um pouco como pequenos serviços que comunicam através de mensagens. Cada entidade tem uma identidade única e um estado interno (se existir). Como serviços ou objetos, as entidades realizam operações quando solicitadas a fazê-lo. Quando uma operação executa, pode atualizar o estado interno da entidade. Pode também chamar os serviços externos e aguardar uma resposta. As entidades comunicam com outras entidades, orquestrações e clientes utilizando mensagens que são implicitamente enviadas através de filas fiáveis. 

Para evitar conflitos, todas as operações numa única entidade são garantidas para executar em série, isto é, uma após a outra.

> [!NOTE]
> Quando uma entidade é invocada, processa a sua carga útil até à conclusão e, em seguida, agenda uma nova execução para ativar assim que as futuras entradas chegarem. Como resultado, os registos de execução da sua entidade podem apresentar uma execução extra após cada invocação de entidade; isto é esperado.

### <a name="entity-id"></a>ID de entidade
As entidades são acedidas através de um identificador único, o ID da *entidade.* Um ID de entidade é simplesmente um par de cordas que identifica unicamente uma instância de entidade. Consiste em:

* **Nome da entidade,** que é um nome que identifica o tipo da entidade. Um exemplo é "Counter". Este nome deve corresponder ao nome da função da entidade que implementa a entidade. Não é sensível ao caso.
* **Chave de entidade,** que é uma cadeia que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome. Um exemplo é um GUID.

Por exemplo, uma `Counter` função de entidade pode ser usada para manter a pontuação num jogo online. Cada instância do jogo tem uma identificação de entidade única, como `@Counter@Game1` e `@Counter@Game2` . Todas as operações que visam uma determinada entidade requerem especificar um ID de entidade como parâmetro.

### <a name="entity-operations"></a>Entity operations (Operações de entidade) ###

Para invocar uma operação numa entidade, especifique:

* **ID de entidade** da entidade-alvo.
* **Nome da operação,** que é uma cadeia que especifica a operação a realizar. Por exemplo, a `Counter` entidade poderia `add` `get` suportar, ou `reset` operações.
* **Entrada de operação**, que é um parâmetro de entrada opcional para o funcionamento. Por exemplo, a operação de adição pode tomar uma quantia número inteiro como entrada.
* **Hora programada**, que é um parâmetro opcional para especificar o tempo de entrega da operação. Por exemplo, uma operação pode ser agendada de forma fiável para ser executada vários dias no futuro.

As operações podem devolver um valor de resultado ou um resultado de erro, como um erro JavaScript ou uma exceção .NET. Este resultado ou erro pode ser observado por orquestrações que chamaram a operação.

Uma operação de entidade também pode criar, ler, atualizar e apagar o estado da entidade. O estado da entidade é sempre duradoura no armazenamento.

## <a name="define-entities"></a>Definir entidades

Atualmente, as duas APIs distintas para entidades determinantes são:

**Sintaxe baseada em funções,** onde as entidades são representadas como funções e operações são explicitamente despachadas pela aplicação. Esta sintaxe funciona bem para entidades com estado simples, poucas operações, ou um conjunto dinâmico de operações como em quadros de aplicação. Esta sintaxe pode ser aborrecida para manter porque não apanha erros de tipo no tempo de compilação.

**Sintaxe baseada na classe (apenas.NET),** onde entidades e operações são representadas por classes e métodos. Esta sintaxe produz um código mais facilmente legível e permite que as operações sejam invocadas de forma tipo segura. A sintaxe baseada na classe é uma camada fina em cima da sintaxe baseada na função, para que ambas as variantes possam ser usadas intercambiavelmente na mesma aplicação.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Exemplo: Sintaxe baseada em funções - C #

O código a seguir é um exemplo de uma entidade simples `Counter` implementada como uma função durável. Esta função define três `add` operações, e cada uma das quais opera num estado `reset` `get` inteiro.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Para obter mais informações sobre a sintaxe baseada na função e como usá-la, consulte a [sintaxe baseada na função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Exemplo: Sintaxe baseada em classe - C #

O exemplo a seguir é uma implementação equivalente da `Counter` entidade utilizando classes e métodos.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

O estado desta entidade é um objeto de `Counter` tipo, que contém um campo que armazena o valor atual do balcão. Para persistir este objeto no armazenamento, é serializado e desercializado pela biblioteca [Json.NET.](https://www.newtonsoft.com/json) 

Para obter mais informações sobre a sintaxe baseada na classe e como usá-la, consulte [as classes de entidades definidoras](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Exemplo: Entidade JavaScript

As entidades duráveis estão disponíveis no JavaScript a partir da versão **1.3.0** do `durable-functions` pacote npm. O código a seguir é a `Counter` entidade implementada como uma função durável escrita no JavaScript.

**Contador/function.jsligado**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Contador/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Exemplo: Entidade Python

O seguinte código é a `Counter` entidade implementada como uma função durável escrita em Python.

**Contador/function.jsligado**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Contador/__init__.py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Entidades de acesso

As entidades podem ser acedidas através de comunicação unidirecional ou bidirecional. A seguinte terminologia distingue as duas formas de comunicação: 

* **Chamar** uma entidade utiliza comunicação bidirecional (ida e volta). Envia uma mensagem de operação à entidade e, em seguida, aguarda a mensagem de resposta antes de continuar. A mensagem de resposta pode fornecer um valor de resultado ou um resultado de erro, como um erro JavaScript ou uma exceção .NET. Este resultado ou erro é então observado pelo autor da chamada.
* **A sinalização de** uma entidade utiliza comunicação unidirecional (fogo e esquecimento). Envias uma mensagem de operação, mas não esperes por uma resposta. Enquanto a mensagem é garantida para ser entregue eventualmente, o remetente não sabe quando e não pode observar qualquer valor de resultado ou erros.

As entidades podem ser acedidas a partir de funções de cliente, de funções orquestradoras ou de funções de entidade. Nem todas as formas de comunicação são apoiadas por todos os contextos:

* A partir de dentro dos clientes, pode sinalizar entidades e pode ler o estado da entidade.
* A partir de dentro de orquestrações, pode sinalizar entidades e pode chamar entidades.
* A partir de entidades, pode sinalizar entidades.

Os exemplos que se seguem ilustram estas várias formas de acesso às entidades.

### <a name="example-client-signals-an-entity"></a>Exemplo: Cliente sinaliza uma entidade

Para aceder a entidades de uma Função Azure ordinária, também conhecida como função de cliente, utilize a [entidade de ligação ao cliente.](durable-functions-bindings.md#entity-client) O exemplo a seguir mostra uma função acionada por fila que sinaliza uma entidade utilizando esta ligação.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Para simplificar, os exemplos a seguir mostram a sintaxe vagamente digitada para aceder a entidades. Em geral, recomendamos que [aceda a entidades através de interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) porque fornece mais verificação de tipo.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

O termo *sinal* significa que a invocação da API da entidade é unidireccionária e assíncrono. Não é possível que um cliente funcione saber quando a entidade processou a operação. Além disso, a função do cliente não pode observar quaisquer valores de resultados ou exceções. 

### <a name="example-client-reads-an-entity-state"></a>Exemplo: Cliente lê um estado de entidade

As funções do cliente também podem consultar o estado de uma entidade, como mostra o seguinte exemplo:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> A Python não suporta atualmente estados de entidades de leitura de um cliente. Use o de um `callEntity` orquestrador.

---

As consultas estatais da entidade são enviadas para a loja de rastreio Durable e devolvem o estado mais recentemente persistido da entidade. Este Estado é sempre um estado "comprometido", ou seja, nunca é um estado intermédio temporário assumido no meio da execução de uma operação. No entanto, é possível que este estado esteja velho em comparação com o estado de memória da entidade. Apenas as orquestrações podem ler o estado de memória de uma entidade, conforme descrito na secção seguinte.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemplo: Sinalização de orquestração e chama uma entidade

As funções orquestradoras podem aceder a entidades utilizando APIs na ligação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger). O seguinte código de exemplo mostra uma função orquestradora a chamar e sinalizar uma `Counter` entidade.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> O JavaScript não suporta atualmente a sinalização de uma entidade de um orquestrador. Em vez disso, utilize `callEntity`.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Apenas as orquestrações são capazes de chamar entidades e obter uma resposta, o que pode ser um valor de retorno ou uma exceção. As funções do cliente que utilizam a [ligação](durable-functions-bindings.md#entity-client) ao cliente só podem sinalizar entidades.

> [!NOTE]
> Chamar uma entidade de uma função orquestradora é semelhante a chamar uma [função](durable-functions-types-features-overview.md#activity-functions) de atividade de uma função orquestradora. A principal diferença é que as funções da entidade são objetos duráveis com um endereço, que é o ID da entidade. A entidade funciona suporte especificando um nome de operação. As funções de atividade, por outro lado, são apátridas e não têm o conceito de operações.

### <a name="example-entity-signals-an-entity"></a>Exemplo: Entidade sinaliza uma entidade

Uma função de entidade pode enviar sinais a outras entidades, ou mesmo a si mesmo, enquanto executa uma operação.
Por exemplo, podemos modificar o exemplo da entidade anterior `Counter` para que envie um sinal "marco atingido" a alguma entidade monitora quando o contador atinge o valor 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python ainda não suporta sinais de entidade-para-entidade. Por favor, use um orquestrador para entidades de sinalização.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Coordenação de entidades (atualmente apenas .NET)

Pode haver alturas em que é preciso coordenar operações em várias entidades. Por exemplo, numa aplicação bancária, poderá ter entidades que representam contas bancárias individuais. Quando transfere fundos de uma conta para outra, deve certificar-se de que a conta de origem tem fundos suficientes. Também deve garantir que as atualizações para as contas de origem e destino são feitas de forma transacional.

### <a name="example-transfer-funds-c"></a>Exemplo: Fundos de transferência (C#)

O seguinte exemplo código transfere fundos entre duas entidades de conta utilizando uma função orquestradora. Coordenar as atualizações das entidades requer a utilização do `LockAsync` método para criar uma secção _crítica_ na orquestração.

> [!NOTE]
> Para simplificar, este exemplo reutiliza a `Counter` entidade definida anteriormente. Numa aplicação real, seria melhor definir uma entidade mais `BankAccount` detalhada.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

Em .NET, `LockAsync` `IDisposable` devoluções , que termina a secção crítica quando eliminada. Este `IDisposable` resultado pode ser usado juntamente com um bloco para obter uma `using` representação sintática da secção crítica.

No exemplo anterior, uma função orquestradora transferiu fundos de uma entidade de origem para uma entidade de destino. O `LockAsync` método bloqueou as entidades de conta de origem e de destino. Este bloqueio garantiu que nenhum outro cliente poderia consultar ou modificar o estado de qualquer uma das contas até que a lógica de orquestração saísse da secção crítica no final da `using` declaração. Este comportamento impede a possibilidade de descoberto da conta de origem.

> [!NOTE] 
> Quando uma orquestração termina, normalmente ou com um erro, quaisquer secções críticas em curso são implicitamente encerradas e todas as fechaduras são libertadas.

### <a name="critical-section-behavior"></a>Comportamento da secção crítica

O `LockAsync` método cria uma secção crítica numa orquestração. Estas secções críticas impedem que outras orquestrações escamemem alterações a um determinado conjunto de entidades. Internamente, a `LockAsync` API envia operações de "bloqueio" às entidades e regressa quando recebe uma mensagem de resposta "lock adquirida" de cada uma dessas mesmas entidades. Tanto o bloqueio como o desbloqueio são operações incorporadas apoiadas por todas as entidades.

Não são permitidas operações de outros clientes numa entidade enquanto estiver num estado fechado. Este comportamento garante que apenas uma instância de orquestração pode bloquear uma entidade de cada vez. Se um chamador tentar invocar uma operação numa entidade enquanto está trancada por uma orquestração, essa operação é colocada numa fila de operação pendente. Não são processadas operações pendentes até que a orquestração de retenção liberte o seu bloqueio.

> [!NOTE] 
> Este comportamento é ligeiramente diferente dos primitivos de sincronização usados na maioria das linguagens de programação, como a `lock` afirmação em C#. Por exemplo, em C#, a `lock` declaração deve ser usada por todos os fios para garantir uma sincronização adequada através de vários fios. As entidades, no entanto, não exigem que todos os chamadores bloqueiem explicitamente uma entidade. Se alguém bloquear uma entidade, todas as outras operações dessa entidade estão bloqueadas e em fila atrás dessa fechadura.

Os bloqueios às entidades são duráveis, pelo que persistem mesmo que o processo de execução seja reciclado. Os cadeados são internamente persistidos como parte do estado duradouro de uma entidade.

Ao contrário das transações, as secções críticas não revertem automaticamente as alterações em caso de erros. Em vez disso, qualquer manipulação de erros, como retrocesso ou reversão, deve ser explicitamente codificada, por exemplo, através da captura de erros ou exceções. Esta escolha de design é intencional. Reverter automaticamente todos os efeitos de uma orquestração é difícil ou impossível em geral, porque as orquestrações podem executar atividades e fazer chamadas para serviços externos que não podem ser revertidos. Além disso, as tentativas de retrocesso podem falhar e exigir mais manipulação de erros.

### <a name="critical-section-rules"></a>Regras de secção crítica

Ao contrário dos primitivos de baixo nível na maioria das linguagens de programação, as secções críticas são *garantidas para não travar*. Para evitar impasses, aplicamos as seguintes restrições: 

* Secções críticas não podem ser aninhadas.
* Secções críticas não podem criar suborquívers.
* As secções críticas só podem chamar entidades que tenham bloqueado.
* As secções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* Secções críticas só podem sinalizar entidades que não bloquearam.

Quaisquer violações destas regras causam um erro de tempo de execução, como `LockingRulesViolationException` em .NET, que inclui uma mensagem que explica que regra foi quebrada.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitas das características de entidades duráveis são inspiradas no [modelo do ator.](https://en.wikipedia.org/wiki/Actor_model) Se já conhece atores, pode reconhecer muitos dos conceitos descritos neste artigo. As entidades duráveis são particularmente semelhantes a [atores virtuais,](https://research.microsoft.com/projects/orleans/)ou grãos, como popularizado pelo [projeto Orleans.](http://dotnet.github.io/orleans/) Por exemplo:

* As entidades duradouras são endereçadas através de uma identificação de entidade.
* As operações de entidades duradouras executam em série, uma de cada vez, para evitar condições de corrida.
* As entidades duradouras são criadas implicitamente quando são chamadas ou sinalizadas.
* Quando não executam operações, as entidades duráveis são silenciosamente descarregadas da memória.

Há algumas diferenças importantes que merecem ser notantes:

* As entidades duradouras priorizam a durabilidade em relação à latência, pelo que podem não ser adequadas para aplicações com requisitos rigorosos de latência.
* Entidades duradouras não têm prazos incorporados para mensagens. Em Orleães, todas as mensagens saem depois de um tempo configurável. A predefinição é 30 segundos.
* As mensagens enviadas entre entidades são entregues de forma fiável e em ordem. Em Orleães, a entrega fiável ou ordenada é suportada para conteúdos enviados através de streams, mas não é garantido para todas as mensagens entre grãos.
* Os padrões de resposta de pedido nas entidades limitam-se a orquestrações. De dentro das entidades, apenas mensagens unidirecionais (também conhecidas como sinalização) são permitidas, como no modelo de ator original, e ao contrário dos grãos em Orleães. 
* Entidades duradouras não estão num impasse. Em Orleães, os impasses podem ocorrer e não se resolvem até que as mensagens se esusem.
* Entidades duradouras podem ser usadas em conjunto com orquestrações duradouras e mecanismos de bloqueio distribuídos. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Leia o guia do Desenvolvedor para entidades duráveis em .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Conheça os centros de tarefas](durable-functions-task-hubs.md)
