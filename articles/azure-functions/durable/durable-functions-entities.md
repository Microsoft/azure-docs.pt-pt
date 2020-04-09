---
title: Entidades duráveis - Funções Azure
description: Saiba quais são as entidades duráveis e como usá-las na extensão de Funções Duráveis para Funções Azure.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4f45ac40e7df865bdb4722d086325096c377cd59
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877547"
---
# <a name="entity-functions"></a>Funções da entidade

As funções da entidade definem as operações de leitura e atualização de pequenas peças de Estado, conhecidas como *entidades duráveis.* Tal como as funções orquestradoras, as funções da entidade são funções com um tipo especial de gatilho, o gatilho da *entidade*. Ao contrário das funções orquestradoras, as funções da entidade gerem explicitamente o estado de uma entidade, em vez de representarem implicitamente o Estado através do fluxo de controlo.
As entidades fornecem um meio para escalonar as aplicações distribuindo o trabalho por muitas entidades, cada uma com um estado modestamente dimensionado.

> [!NOTE]
> As funções da entidade e funcionalidades conexas só estão disponíveis nas Funções Duráveis 2.0 ou superior.

## <a name="general-concepts"></a>Conceitos gerais

As entidades comportam-se um pouco como pequenos serviços que comunicam através de mensagens. Cada entidade tem uma identidade única e um estado interno (se existir). Tal como serviços ou objetos, as entidades realizam operações quando são solicitadas a fazê-lo. Quando uma operação executa, pode atualizar o estado interno da entidade. Também pode chamar serviços externos e esperar por uma resposta. As entidades comunicam com outras entidades, orquestrações e clientes através da utilização de mensagens que são implicitamente enviadas através de filas fiáveis. 

Para evitar conflitos, todas as operações numa única entidade são garantidas para executar em série, isto é, uma após a outra. 

### <a name="entity-id"></a>ID da entidade
As entidades são acedidas através de um identificador único, o ID da *entidade.* Um ID de entidade é simplesmente um par de cordas que identifica exclusivamente uma instância de entidade. Consiste num:

* Nome da **entidade,** que é um nome que identifica o tipo de entidade. Um exemplo é "Contador". Este nome deve coincidir com o nome da função entidade que implementa a entidade. Não é sensível ao caso.
* **Chave da entidade,** que é uma cadeia que identifica exclusivamente a entidade entre todas as outras entidades com o mesmo nome. Um exemplo é um GUID.

Por exemplo, `Counter` uma função de entidade pode ser usada para manter a pontuação num jogo online. Cada instância do jogo tem uma identificação de entidade única, como `@Counter@Game1` e `@Counter@Game2`. Todas as operações que visam uma determinada entidade exigem especificar uma identidade de entidade como parâmetro.

### <a name="entity-operations"></a>Entity operations (Operações de entidade) ###

Para invocar uma operação numa entidade, especifique:

* **Identificação** da entidade-alvo.
* Nome de **funcionamento**, que é uma cadeia que especifica a operação a executar. Por exemplo, `Counter` a `add`entidade `get`poderia `reset` apoiar , ou operações.
* **Entrada de funcionamento**, que é um parâmetro de entrada opcional para a operação. Por exemplo, a operação de adição pode ter uma quantidade de inteiro como entrada.
* **Hora programada**, que é um parâmetro opcional para especificar o tempo de entrega da operação. Por exemplo, uma operação pode ser programada de forma fiável para ser executada vários dias no futuro.

As operações podem devolver um valor de resultado ou um resultado de erro, como um erro Do JavaScript ou uma exceção .NET. Este resultado ou erro pode ser observado por orquestrações que chamaram a operação.

Uma operação de entidade também pode criar, ler, atualizar e apagar o estado da entidade. O estado da entidade é sempre persistente no armazenamento.

## <a name="define-entities"></a>Definir entidades

Atualmente, as duas APIs distintas para a definição de entidades são:

**Sintaxe baseada em funções,** onde as entidades são representadas como funções e as operações são explicitamente despachadas pela aplicação. Esta sintaxe funciona bem para entidades com estado simples, poucas operações, ou um conjunto dinâmico de operações como nos quadros de aplicações. Esta sintaxe pode ser aborrecida de manter porque não apanha erros de tipo no momento da compilação.

**Sintaxe baseada em classe (apenas EM NET),** onde entidades e operações são representadas por classes e métodos. Esta sintaxe produz código mais facilmente legível e permite que as operações sejam invocadas de forma segura. A sintaxe baseada na classe é uma camada fina em cima da sintaxe baseada na função, por isso ambas as variantes podem ser usadas alternadamente na mesma aplicação.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Exemplo: Sintaxe baseada em funções - C #

O seguinte código é um `Counter` exemplo de uma entidade simples implementada como uma função durável. Esta função define `add`três `reset`operações, e, `get`cada uma das quais opera num estado inteiro.

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

O exemplo seguinte é uma `Counter` implementação equivalente da entidade utilizando classes e métodos.

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

O estado desta entidade é `Counter`um objeto de tipo, que contém um campo que armazena o valor atual do balcão. Para persistir este objeto no armazenamento, é serializado e desserializado pela biblioteca [Json.NET.](https://www.newtonsoft.com/json) 

Para obter mais informações sobre a sintaxe baseada na classe e como usá-la, consulte [as classes de entidades Definintes.](durable-functions-dotnet-entities.md#defining-entity-classes)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Exemplo: Entidade JavaScript

Entidades duráveis estão disponíveis no JavaScript a partir `durable-functions` da versão **1.3.0** do pacote npm. O seguinte código `Counter` é a entidade implementada como uma função durável escrita no JavaScript.

**Contador/função.json**
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

---

## <a name="access-entities"></a>Entidades de acesso

As entidades podem ser acedidas através de comunicação de sentido único ou bidirecional. A seguinte terminologia distingue as duas formas de comunicação: 

* **Chamar** uma entidade usa comunicação bidirecional (ida e volta). Envia uma mensagem de operação à entidade e depois aguarda a mensagem de resposta antes de continuar. A mensagem de resposta pode fornecer um valor de resultado ou um resultado de erro, como um erro JavaScript ou uma exceção .NET. Este resultado ou erro é então observado pelo chamador.
* **Sinalizar** uma entidade utiliza comunicação de sentido único (fogo e esquecimento). Enviauma mensagem de operação, mas não espere sã e reata. Embora a mensagem seja garantida para ser entregue eventualmente, o remetente não sabe quando e não consegue observar qualquer valor ou erro do resultado.

As entidades podem ser acedidas a partir de funções de cliente, a partir de funções orquestradoras, ou de dentro das funções da entidade. Nem todas as formas de comunicação são apoiadas por todos os contextos:

* A partir de dentro dos clientes, pode sinalizar entidades e pode ler o estado da entidade.
* A partir de orquestrações, pode sinalizar entidades e pode ligar para entidades.
* De dentro de entidades, pode sinalizar entidades.

Os exemplos que se seguem ilustram estas várias formas de aceder a entidades.

### <a name="example-client-signals-an-entity"></a>Exemplo: Cliente sinaliza uma entidade

Para aceder a entidades de uma Função Azure ordinária, que também é conhecida como função cliente, utilize a [entidade de vinculação](durable-functions-bindings.md#entity-client)do cliente. O exemplo seguinte mostra uma função acionada pela fila que sinaliza uma entidade que utiliza esta ligação.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Para a simplicidade, os seguintes exemplos mostram a sintaxe dactilografada vagamente para aceder a entidades. Em geral, recomendamos que [aceda a entidades através](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) de interfaces porque fornece mais verificação de tipo.

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

---

O *sinal* de termo significa que a invocação da API da entidade é de sentido único e assíncrono. Não é possível que uma função do cliente saiba quando a entidade processou a operação. Além disso, a função do cliente não pode observar quaisquer valores ou exceções de resultados. 

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

---

As consultas estatais da entidade são enviadas para a loja de rastreio Durável e devolvem o estado mais recentemente persistido da entidade. Este Estado é sempre um estado "comprometido", ou seja, nunca é um estado temporário intermédio assumido no meio da execução de uma operação. No entanto, é possível que este estado esteja velho em comparação com o estado de memória da entidade. Apenas as orquestrações podem ler o estado de memória de uma entidade, como descrito na secção seguinte.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Exemplo: Orquestração sinaliza e chama uma entidade

As funções de orquestrador podem aceder a entidades utilizando APIs na ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger). O seguinte código de exemplo mostra uma `Counter` função orquestradora chamando e sinalizando uma entidade.

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
> O JavaScript não suporta atualmente sinalizar uma entidade de um orquestrador. Em vez disso, utilize `callEntity`.

---

Apenas as orquestrações são capazes de chamar entidades e obter uma resposta, o que pode ser um valor de retorno ou uma exceção. As funções do cliente que utilizam a [ligação](durable-functions-bindings.md#entity-client) do cliente só podem sinalizar entidades.

> [!NOTE]
> Chamar uma entidade de uma função de orquestrador é semelhante a chamar uma [função](durable-functions-types-features-overview.md#activity-functions) de atividade a partir de uma função orquestradora. A principal diferença é que as funções da entidade são objetos duráveis com um endereço, que é o ID da entidade. As funções da entidade suportam especificar um nome de operação. As funções de atividade, por outro lado, são apátridas e não têm o conceito de operações.

### <a name="example-entity-signals-an-entity"></a>Exemplo: Entidade sinaliza uma entidade

Uma função de entidade pode enviar sinais a outras entidades, ou mesmo a si mesma, enquanto executa uma operação.
Por exemplo, podemos `Counter` modificar o exemplo da entidade anterior para que envie um sinal "marco-alcance" a alguma entidade de monitorização quando o contador atinge o valor 100.

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

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Coordenação de entidades (atualmente apenas .NET)

Pode haver alturas em que é preciso coordenar operações em várias entidades. Por exemplo, num pedido bancário, pode ter entidades que representam contas bancárias individuais. Quando transfere fundos de uma conta para outra, deve garantir que a conta fonte tem fundos suficientes. Deve também garantir que as atualizações tanto nas contas de origem como para as contas de destino são feitas de forma consistente transacional.

### <a name="example-transfer-funds-c"></a>Exemplo: Fundos de transferência (C#)

O código de exemplo seguinte transfere fundos entre duas entidades de conta utilizando uma função de orquestrador. Coordenar atualizações de entidades `LockAsync` requer a utilização do método para criar uma _secção crítica_ na orquestração.

> [!NOTE]
> Para a simplicidade, este exemplo `Counter` reutiliza a entidade previamente definida. Numa aplicação real, seria melhor definir `BankAccount` uma entidade mais detalhada.

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

Em `LockAsync` .NET, `IDisposable`devoluções , que termina a secção crítica quando eliminada. Este `IDisposable` resultado pode ser `using` usado juntamente com um bloco para obter uma representação sintática da secção crítica.

No exemplo anterior, uma função orquestradora transferiu fundos de uma entidade fonte para uma entidade de destino. O `LockAsync` método bloqueou tanto as entidades de origem como as entidades da conta de destino. Este bloqueio garantiu que nenhum outro cliente poderia consultar ou modificar o estado de qualquer conta `using` até que a lógica da orquestração saísse da secção crítica no final do comunicado. Este comportamento impede a possibilidade de descoberto a partir da conta fonte.

> [!NOTE] 
> Quando uma orquestração termina, normalmente ou com um erro, quaisquer secções críticas em curso são implicitamente terminadas e todas as fechaduras são libertadas.

### <a name="critical-section-behavior"></a>Comportamento da secção crítica

O `LockAsync` método cria uma secção crítica numa orquestração. Estas secções críticas impedem que outras orquestrações alterem-se num conjunto de entidades especificada. Internamente, `LockAsync` a API envia operações de "bloqueio" às entidades e devoluções quando recebe uma mensagem de resposta "lock acquired" de cada uma dessas mesmas entidades. Tanto o bloqueio como o desbloqueio são operações incorporadas apoiadas por todas as entidades.

Nenhuma operação de outros clientes é permitida numa entidade enquanto está em estado fechado. Este comportamento garante que apenas uma instância de orquestração pode bloquear uma entidade de cada vez. Se um ouvinte tentar invocar uma operação numa entidade enquanto está bloqueada por uma orquestração, essa operação é colocada numa fila de operações pendente. Não são processadas operações pendentes até que a orquestração de detenção abra o bloqueio.

> [!NOTE] 
> Este comportamento é ligeiramente diferente dos primitivos de sincronização usados `lock` na maioria das linguagens de programação, como a afirmação em C#. Por exemplo, em C#, a `lock` declaração deve ser utilizada por todos os fios para garantir uma sincronização adequada através de vários fios. As entidades, no entanto, não exigem que todos os chamadores bloqueiem explicitamente uma entidade. Se qualquer chamada bloquear uma entidade, todas as outras operações nessa entidade estão bloqueadas e em fila atrás dessa fechadura.

Os bloqueios às entidades são duráveis, pelo que persistem mesmo que o processo de execução seja reciclado. As fechaduras são internamente persistidas como parte do estado durável de uma entidade.

Ao contrário das transações, as secções críticas não revertem automaticamente as alterações em caso de erros. Em vez disso, qualquer manipulação de erros, como o retrocesso ou a retenção, deve ser explicitamente codificado, por exemplo, capturando erros ou exceções. Esta escolha de design é intencional. Reverter automaticamente todos os efeitos de uma orquestração é difícil ou impossível em geral, porque as orquestrações podem executar atividades e fazer chamadas para serviços externos que não podem ser relançados. Além disso, as tentativas de retrocesso podem falhar e exigir mais manipulação de erros.

### <a name="critical-section-rules"></a>Regras de secção crítica

Ao contrário dos primitivos de bloqueio de baixo nível na maioria das línguas de programação, as secções críticas são *garantidas para não travar*. Para evitar impasses, aplicamos as seguintes restrições: 

* Secções críticas não podem ser aninhadas.
* Secções críticas não podem criar suborquestrações.
* Secções críticas podem ligar apenas para entidades que bloquearam.
* Secções críticas não podem chamar a mesma entidade usando várias chamadas paralelas.
* Secções críticas podem sinalizar apenas entidades que não trancaram.

Quaisquer violações destas regras causam um `LockingRulesViolationException` erro de tempo de execução, como em .NET, que inclui uma mensagem que explica que regra foi quebrada.

## <a name="comparison-with-virtual-actors"></a>Comparação com atores virtuais

Muitas das entidades duráveis são inspiradas no modelo do [ator.](https://en.wikipedia.org/wiki/Actor_model) Se já conhece os atores, pode reconhecer muitos dos conceitos descritos neste artigo. As entidades duráveis são particularmente semelhantes a [atores virtuais](https://research.microsoft.com/projects/orleans/), ou grãos, popularizados pelo [projeto de Orleães.](http://dotnet.github.io/orleans/) Por exemplo:

* As entidades duráveis são endereçadas através de uma identificação da entidade.
* As operações de entidades duráveis executam em série, uma de cada vez, para evitar as condições de corrida.
* As entidades duráveis são criadas implicitamente quando são chamadas ou sinalizadas.
* Quando não executam as operações, as entidades duráveis são silenciosamente descarregadas da memória.

Há algumas diferenças importantes que merecem ser notadas:

* As entidades duráveis priorizam a durabilidade em detrimento da latência, pelo que podem não ser adequadas para aplicações com requisitos rigorosos de latência.
* Entidades duráveis não têm intervalos incorporados para mensagens. Em Orleães, todas as mensagens estão esgotadas depois de um tempo configurável. A predefinição é 30 segundos.
* As mensagens enviadas entre entidades são entregues de forma fiável e em ordem. Em Orleães, a entrega fiável ou ordenada é suportada para conteúdos enviados através de streams, mas não é garantido para todas as mensagens entre grãos.
* Os padrões de resposta a pedidos nas entidades limitam-se a orquestrações. De dentro de entidades, apenas é permitida a mensagem de sentido único (também conhecida como sinalização), como no modelo original do ator, e ao contrário dos grãos em Orleães. 
* Entidades duráveis não bloqueiam. Em Orleães, os impasses podem ocorrer e não resolvem até que as mensagens se estem.
* As entidades duráveis podem ser utilizadas em conjunto com orquestrações duráveis e mecanismos de bloqueio distribuídos. 


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Leia o guia do Programador a entidades duráveis em .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Conheça os centros de tarefas](durable-functions-task-hubs.md)
