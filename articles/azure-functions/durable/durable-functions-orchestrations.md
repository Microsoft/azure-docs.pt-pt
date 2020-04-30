---
title: Orquestrações Duráveis - Funções Azure
description: Introdução à função de orquestração para funções duráveis Azure.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79241361"
---
# <a name="durable-orchestrations"></a>Orquestrações Duráveis

Funções Duráveis é uma extensão das [Funções Azure.](../functions-overview.md) Você pode usar uma *função de orquestrador* para orquestrar a execução de outras funções Duráveis dentro de uma aplicação de função. As funções de orquestrador têm as seguintes características:

* Funções de orquestração definem fluxos de trabalho de função usando código processual. Não são necessários esquemas declarativos ou designers.
* As funções de orquestrador podem chamar outras funções duráveis sincronizada e assíncrona. A saída das funções chamadas pode ser guardada de forma fiável para variáveis locais.
* As funções de orquestrador são duráveis e fiáveis. O progresso da execução é automaticamente posto em controlo quando a função "aguarda" ou "yields". O estado local nunca se perde quando o processo recicla ou o VM reinicia.
* As funções de orquestrador podem ser de longa duração. O tempo de vida total de uma instância de *orquestração* pode ser segundos, dias, meses ou sem fim.

Este artigo dá-lhe uma visão geral das funções orquestradoras e como podem ajudá-lo a resolver vários desafios de desenvolvimento de aplicações. Se ainda não estiver familiarizado com os tipos de funções disponíveis numa aplicação de Funções Duráveis, leia primeiro o artigo dos tipos de [Função Durável.](durable-functions-types-features-overview.md)

## <a name="orchestration-identity"></a>Identidade de orquestração

Cada *instância* de uma orquestração tem um identificador de instância (também conhecido como identificação de *instância).* Por padrão, cada id de instância é um GUID autogerado. No entanto, as iDs de instância também podem ser qualquer valor de cadeia gerado pelo utilizador. Cada instância de orquestração id deve ser única dentro de um centro de [tarefas](durable-functions-task-hubs.md).

Seguem-se algumas regras sobre iDs de instância:

* As identificações devem estar entre 1 e 256 caracteres.
* Os IDs de `@`instância não devem começar com .
* Os IDs de `/` `\`instância `#`não `?` devem conter, ou caracteres.
* Os IDs de instância não devem conter caracteres de controlo.

> [!NOTE]
> Geralmente, recomenda-se a utilização de IDs de instância autogerada sempre que possível. Os IDs de instância gerados pelo utilizador destinam-se a cenários em que existe um mapeamento de um para um entre uma instância de orquestração e alguma entidade externa específica para aplicações, como uma ordem de compra ou um documento.

A identificação por exemplo de uma orquestração é um parâmetro necessário para a maioria das operações de [gestão](durable-functions-instance-management.md)de exemplos. São também importantes para diagnósticos, como pesquisar através de dados de rastreio de [orquestração](durable-functions-diagnostics.md#application-insights) em Application Insights para fins de resolução de problemas ou análises. Por esta razão, recomenda-se que guarde iDs de instância geradas para algum local externo (por exemplo, uma base de dados ou em registos de aplicações) onde possam ser facilmente referenciados mais tarde.

## <a name="reliability"></a>Fiabilidade

As funções de orquestrador mantêm de forma fiável o seu estado de execução utilizando o padrão de design de fornecimento de [eventos.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) Em vez de armazenar diretamente o estado atual de uma orquestração, o Quadro de Tarefas Duráveis utiliza uma loja apenas de apêndice para gravar toda a série de ações que a orquestração de funções toma. Uma loja só para apêndices tem muitos benefícios em comparação com "dumping" do estado de funcionar integral. Os benefícios incluem o aumento do desempenho, escalabilidade e capacidade de resposta. Você também obtém uma eventual consistência para dados transacionais e pistas de auditoria completas e histórico. Os trilhos de auditoria apoiam ações compensatórias fiáveis.

As Funções Duráveis utilizam o fornecimento de eventos de forma transparente. Nos bastidores, `await` o operador `yield` (C#) ou (JavaScript) numa função de orquestrador rende o controlo do fio orquestrador de volta ao despacho do Quadro de Tarefas Duráveis. O expedidor compromete então quaisquer novas ações que a função de orquestrador programou (como chamar uma ou mais funções infantis ou agendar um temporizador durável) para armazenamento. A ação transparente compromete-se a ser executada na história da orquestração. A história está guardada numa mesa de armazenamento. A ação de compromisso adiciona então mensagens a uma fila para agendar o trabalho real. Neste ponto, a função de orquestrador pode ser descarregada da memória.

Quando uma função de orquestração é dada mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou expira um temporizador durável), o orquestrador acorda e reexecuta toda a função desde o início para reconstruir o estado local. Durante a repetição, se o código tentar chamar uma função (ou fazer qualquer outro trabalho de sincronia), o Quadro de Tarefas Duráveis consulta o histórico de execução da orquestração atual. Se verificar que a [função](durable-functions-types-features-overview.md#activity-functions) de atividade já executou e obteve um resultado, repete o resultado dessa função e o código orquestrador continua a funcionar. A repetição continua até que o código de função esteja terminado ou até ter programado novos trabalhos de asincronização.

> [!NOTE]
> Para que o padrão de repetição funcione corretamente e de forma fiável, o código de função orquestrador deve ser *determinista*. Para obter mais informações sobre restrições de código para funções orquestradoras, consulte o tópico de restrições de código de [função orquestrador.](durable-functions-code-constraints.md)

> [!NOTE]
> Se uma função de orquestrador emite mensagens de registo, o comportamento de repetição pode fazer com que as mensagens de registo duplicadas sejam emitidas. Consulte o tópico [de Registo](durable-functions-diagnostics.md#logging) para saber mais sobre o porquê deste comportamento ocorrer e como conviver com ele.

## <a name="orchestration-history"></a>História da orquestração

O comportamento de fornecimento de eventos do Quadro de Tarefas Duráveis é estreitamente associado ao código de função orquestrador que escreve. Suponha que tenha uma função de orquestrador de correntede atividade, como a seguinte função orquestradora:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

---

Em `await` cada declaração (C#) ou `yield` (JavaScript), o Quadro de Tarefas Duráveis verifica o estado de execução da função em algum backend de armazenamento durável (armazenamento tipicamente Azure Table). Este estado é o que é referido como a história da *orquestração.*

### <a name="history-table"></a>Tabela de história

De um modo geral, o quadro de tarefas duráveis faz o seguinte em cada ponto de verificação:

1. Guarda o histórico de execução em mesas de armazenamento Azure.
2. Enfilas mensagens para funções que o orquestrador quer invocar.
3. Enfilas mensagens para o &mdash; próprio orquestrador, por exemplo, mensagens de temporizador duráveis.

Uma vez concluído o ponto de verificação, a função de orquestrador é livre de ser removida da memória até que haja mais trabalho para o fazer.

> [!NOTE]
> O Azure Storage não fornece quaisquer garantias transacionais entre guardar dados em armazenamento de mesas e filas. Para lidar com falhas, o fornecedor de armazenamento de Funções Duráveis utiliza eventuais padrões de *consistência.* Estes padrões garantem que não se perdem dados se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, a história da função mostrada anteriormente se parece com a seguinte tabela no Armazenamento de Mesa Azure (abreviada para fins de ilustração):

| Chave partição (Instanceid)                     | Tipo de evento             | Carimbo de data/hora               | Input | Nome             | Resultado                                                    | Estado |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Execução Iniciada      | 2017-05-05T18:45:28.852Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TarefaSAgendadas         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorConcluído | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TarefaConcluída         | 2017-05-05T18:45:34.201Z |       |                  | "Olá Tóquio!""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TarefaSAgendadas         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorConcluído | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TarefaConcluída         | 2017-05-05T18:45:34.763Z |       |                  | "Olá Seattle!""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TarefaSAgendadas         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorConcluído | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TarefaConcluída         | 2017-05-05T18:45:34.919Z |       |                  | "Olá Londres!""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrquestradorConcluído | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | Execução Concluída    | 2017-05-05T18:45:35.044Z |       |                  | "["Olá Tóquio!"","Olá Seattle!"""Olá Londres!"]" | Concluído           |

Algumas notas sobre os valores da coluna:

* **PartitionKey**: Contém a identificação da instância da orquestração.
* **Tipo**de evento : Representa o tipo do evento. Pode ser um dos seguintes tipos:
  * **OrquestraçãoIniciada**: A função de orquestrador retomou-se de um aguardado ou está a decorrer pela primeira vez. A `Timestamp` coluna é utilizada para preencher `CurrentUtcDateTime` o valor determinístico para as APIs (.NET) e `currentUtcDateTime` (JavaScript).
  * **ExecuçãoIniciada**: A função de orquestrador começou a ser executada pela primeira vez. Este evento também contém a `Input` entrada de função na coluna.
  * **TarefaSAgendadas**: Uma função de atividade foi programada. O nome da função de `Name` atividade é capturado na coluna.
  * **TarefaConcluída**: Uma função de atividade concluída. O resultado da função `Result` está na coluna.
  * **TimerCreated**: Foi criado um temporizador durável. A `FireAt` coluna contém o tempo de UTC programado em que o temporizador expira.
  * **TimerFired:** Um temporizador durável disparado.
  * **EventRaised**: Um evento externo foi enviado para a instância de orquestração. A `Name` coluna captura o nome do `Input` evento e a coluna captura a carga útil do evento.
  * **OrchestratorCompleted**: A função de orquestrador aguardava.
  * **ContinueAsNew**: A função orquestradora completou-se e recomeçou-se com um estado novo. A `Result` coluna contém o valor, que é usado como entrada na instância reiniciada.
  * **ExecuçãoConcluída:** A função de orquestrador correu para a conclusão (ou falhou). As saídas da função ou os erros `Result` são armazenados na coluna.
* **Carimbo**de tempo : O carimbo temporal utc do evento de história.
* **Nome**: O nome da função invocada.
* **Entrada**: A entrada formatada JSON da função.
* **Resultado**: A saída da função; isto é, o seu valor de retorno.

> [!WARNING]
> Embora seja útil como ferramenta de depuração, não tome nenhuma dependência nesta mesa. Pode mudar à medida que a extensão das Funções Duráveis evolui.

Sempre que a função `await` recomeça a `yield` partir de um (C#) ou (JavaScript), o Quadro de Tarefas Duráveis repete a função orquestradora do zero. Em cada recandidatura, consulta o histórico de execução para determinar se a operação asincronizada atual ocorreu.  Se a operação tiver ocorrido, a estrutura repete imediatamente a saída `await` dessa operação e `yield` passa para a seguinte (C#) ou (JavaScript). Este processo continua até que toda a história tenha sido reproduzida. Uma vez reproduzida a história atual, as variáveis locais terão sido restauradas aos seus valores anteriores.

## <a name="features-and-patterns"></a>Características e padrões

As secções seguintes descrevem as características e padrões das funções orquestradoras.

### <a name="sub-orchestrations"></a>Sub-orquestrações

As funções de orquestrador podem chamar funções de atividade, mas também outras funções orquestradoras. Por exemplo, você pode construir uma orquestração maior a partir de uma biblioteca de funções orquestradoras. Ou, pode executar várias instâncias de uma função orquestradora em paralelo.

Para mais informações e, por exemplo, consulte o artigo [sub-orquestrações.](durable-functions-sub-orchestrations.md)

### <a name="durable-timers"></a>Tempors duráveis

As orquestrações podem programar *tempos duráveis* para implementar atrasos ou para configurar o tratamento do timeout em ações de asincronização. Utilize tempors duráveis em funções `Task.Delay` de orquestrador `setTimeout()` `setInterval()` em vez de `Thread.Sleep` e (C#) ou (JavaScript).

Para mais informações e, por exemplo, consulte o artigo dos [temporizadores Duráveis.](durable-functions-timers.md)

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem esperar por eventos externos para atualizar uma instância de orquestração. Esta função Funções Duráveis é muitas vezes útil para lidar com uma interação humana ou outras chamadas externas.

Para mais informações e, por exemplo, consulte o artigo [Eventos Externos.](durable-functions-external-events.md)

### <a name="error-handling"></a>Processamento de erros

As funções de orquestrador podem utilizar as funcionalidades de manipulação de erros da linguagem de programação. Padrões existentes `try` / `catch` como são suportados no código de orquestração.

As funções de orquestrador também podem adicionar políticas de retry às funções de atividade ou sub-orquestrador a que chamam. Se uma função de atividade ou sub-orquestrador falhar com uma exceção, a política de retry especificada pode automaticamente atrasar e voltar a executar até um número especificado de vezes.

> [!NOTE]
> Se houver uma exceção não tratada numa função orquestradora, `Failed` a instância de orquestração completar-se-á num estado. Um caso de orquestração não pode ser julgado novamente uma vez que falhou.

Para mais informações e, por exemplo, consulte o artigo de manipulação de [erros.](durable-functions-error-handling.md)

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Secções críticas (Funções Duráveis 2.x, atualmente apenas .NET)

Os casos de orquestração são de um fio único, por isso não é necessário preocuparmo-nos com as condições de raça *dentro* de uma orquestração. No entanto, as condições de regata são possíveis quando as orquestrações interagem com sistemas externos. Para mitigar as condições de raça ao interagir com sistemas `LockAsync` externos, as funções de orquestração podem definir *secções críticas* usando um método em .NET.

O seguinte código de amostra mostra uma função orquestradora que define uma secção crítica. Entra na secção crítica `LockAsync` utilizando o método. Este método requer a passagem de uma ou mais referências a uma [Entidade Durável,](durable-functions-entities.md)que gere duramente o estado de bloqueio. Apenas uma única instância desta orquestração pode executar o código na secção crítica de cada vez.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

O `LockAsync` adquire o bloqueio durável e `IDisposable` devolve um que termina a secção crítica quando eliminado. Este `IDisposable` resultado pode ser `using` usado juntamente com um bloco para obter uma representação sintática da secção crítica. Quando uma função de orquestrador entra numa secção crítica, apenas uma instância pode executar esse bloco de código. Quaisquer outras instâncias que tentem entrar na secção crítica serão bloqueadas até que a instância anterior saia da secção crítica.

A funcionalidade da secção crítica também é útil para coordenar alterações a entidades duráveis. Para obter mais informações sobre secções críticas, consulte o tema ["Coordenação de Entidades" das entidades Duráveis.](durable-functions-entities.md#entity-coordination)

> [!NOTE]
> As secções críticas estão disponíveis nas Funções Duráveis 2.0 ou superior. Atualmente, apenas as orquestrações .NET implementam esta funcionalidade.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Chamando pontos finais HTTP (Funções Duráveis 2.x)

As funções de orquestrador não estão autorizadas a fazer I/O, como descrito nas restrições de código de [função orquestradora](durable-functions-code-constraints.md). A supor típica para esta limitação é embrulhar qualquer código que precise fazer I/O numa função de atividade. As orquestrações que interagem com sistemas externos usam frequentemente funções de atividade para fazer chamadas HTTP e devolver o resultado à orquestração.

# <a name="c"></a>[C #](#tab/csharp)

Para simplificar este padrão comum, as `CallHttpAsync` funções orquestradoras podem usar o método para invocar http APIs diretamente.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Além de apoiar padrões básicos de pedido/resposta, o método suporta o manuseamento automático de padrões de sondagens async HTTP 202 comuns, e também suporta a autenticação com serviços externos utilizando [identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md)

Para mais informações e para exemplos detalhados, consulte o artigo de [funcionalidades HTTP.](durable-functions-http-features.md)

> [!NOTE]
> Chamar pontos finais HTTP diretamente das funções de orquestrador está disponível nas Funções Duráveis 2.0 ou superior.

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. A recomendação é passar numa série de objetos ou objetos compósitos.

# <a name="c"></a>[C #](#tab/csharp)

Em .NET também pode utilizar objetos [ValueTuples.](https://docs.microsoft.com/dotnet/csharp/tuples) A amostra que se segue está a utilizar novas funcionalidades de [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) adicionadas com [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="activity"></a>Atividade

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Restrições de código do orquestrador](durable-functions-code-constraints.md)
