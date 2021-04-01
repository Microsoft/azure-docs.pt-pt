---
title: Orquestrações Duradouras - Funções Azure
description: Introdução ao recurso de orquestração para Funções Duradouras Azure.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97845422"
---
# <a name="durable-orchestrations"></a>Orquestrações Duradouras

Funções Duradouras é uma extensão das [Funções Azure](../functions-overview.md). Pode utilizar uma *função orquestradora* para orquestrar a execução de outras funções duráveis dentro de uma aplicação de função. As funções do orquestrador têm as seguintes características:

* As funções do orquestrador definem os fluxos de trabalho das funções utilizando código processual. Não são necessários esquemas declarativos ou designers.
* As funções orquestradoras podem chamar outras funções duráveis de forma sincronizada e assíncrona. A saída das funções chamadas pode ser guardada de forma fiável para variáveis locais.
* As funções de orquestrador são duráveis e fiáveis. O progresso da execução é automaticamente posto em controlo quando a função "aguarda" ou "rendimentos". O estado local nunca se perde quando o processo recicla ou o VM reinicia.
* As funções de orquestrador podem ser de longa duração. O tempo de vida total de uma instância de *orquestração* pode ser segundos, dias, meses ou intermináveis.

Este artigo dá-lhe uma visão geral das funções do orquestrador e como eles podem ajudá-lo a resolver vários desafios de desenvolvimento de aplicações. Se ainda não estiver familiarizado com os tipos de funções disponíveis numa aplicação Funções Duradouras, leia primeiro o artigo [dos tipos de Função Durável.](durable-functions-types-features-overview.md)

## <a name="orchestration-identity"></a>Identidade de orquestração

Cada *instância* de uma orquestração tem um identificador de instância (também conhecido como *um iD de exemplo).* Por predefinição, cada identificação de cada instância é um GUID autogerido. No entanto, os IDs de instância também podem ser qualquer valor de cadeia gerado pelo utilizador. Cada identificação de instância de orquestração deve ser único dentro de um [centro de tarefas](durable-functions-task-hubs.md).

Seguem-se algumas regras relativas a iDs de exemplo:

* As identificações de instância devem ter entre 1 e 256 caracteres.
* Os IDs de instância não devem começar com `@` .
* Os IDs de instância não devem `/` `\` conter, ou `#` `?` caracteres.
* Os IDs de instância não devem conter caracteres de controlo.

> [!NOTE]
> É geralmente recomendado utilizar iDs de caso autogeridos sempre que possível. Os IDs de instância gerados pelo utilizador destinam-se a cenários em que existe um mapeamento de um para um entre uma instância de orquestração e alguma entidade externa específica da aplicação, como uma ordem de compra ou um documento.

O ID de exemplo de uma orquestração é um parâmetro necessário para a maioria das [operações de gestão de exemplos](durable-functions-instance-management.md). São também importantes para diagnósticos, como [a pesquisa através de dados de rastreio de orquestração](durable-functions-diagnostics.md#application-insights) em Application Insights para efeitos de resolução de problemas ou analíticos. Por esta razão, recomenda-se guardar iDs de exemplo gerados para alguma localização externa (por exemplo, uma base de dados ou em registos de aplicações) onde podem ser facilmente referenciados mais tarde.

## <a name="reliability"></a>Fiabilidade

As funções do orquestrador mantêm de forma fiável o seu estado de execução utilizando o [padrão de](/azure/architecture/patterns/event-sourcing) design de fornecimento de eventos. Em vez de armazenar diretamente o estado atual de uma orquestração, o Quadro de Tarefas Durable usa uma loja só para gravar a série completa de ações que a orquestração de funções toma. Uma loja só para apêndices tem muitos benefícios em comparação com o "dumping" do estado de tempo integral. Os benefícios incluem o aumento do desempenho, escalabilidade e capacidade de resposta. Você também obtém uma consistência eventual para dados transacionais e pistas de auditoria completa e histórico. Os trilhos de auditoria apoiam ações compensatórias fiáveis.

Funções Duradouras utiliza o fornecimento de eventos de forma transparente. Nos bastidores, o `await` operador (C#) ou `yield` (JavaScript/Python) numa função orquestradora rende o controlo da linha do orquestrador de volta ao despacho durável task framework. O expedidor compromete então quaisquer novas ações que a função do orquestrador tenha programado (como chamar uma ou mais funções infantis ou agendar um temporizador durável) para o armazenamento. A ação transparente compromete-se a aderir à história da execução da instância de orquestração. A história está guardada numa mesa de armazenamento. A ação de compromisso adiciona então mensagens a uma fila para agendar o trabalho real. Neste ponto, a função orquestradora pode ser descarregada da memória.

Quando uma função de orquestração é dada mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador acorda e re-executa toda a função desde o início para reconstruir o estado local. Durante a repetição, se o código tentar convocar uma função (ou fazer qualquer outro trabalho de assídua), o Quadro de Tarefas Durable consulta o histórico de execução da orquestração atual. Se descobrir que a [função](durable-functions-types-features-overview.md#activity-functions) de atividade já executou e deu um resultado, repete o resultado dessa função e o código do orquestrador continua a funcionar. A repetição continua até que o código de função esteja terminado ou até que tenha programado um novo trabalho de async.

> [!NOTE]
> Para que o padrão de repetição funcione correta e de forma fiável, o código de função do orquestrador deve ser *determinístico*. Para obter mais informações sobre as restrições de código para funções de orquestradores, consulte o tópico de restrições do código de função do [orquestrador.](durable-functions-code-constraints.md)

> [!NOTE]
> Se uma função de orquestrador emitir mensagens de registo, o comportamento de repetição pode causar a emissão de mensagens de registo duplicadas. Consulte o tópico [de Registo para](durable-functions-diagnostics.md#app-logging) saber mais sobre o porquê deste comportamento e como trabalhar em torno dele.

## <a name="orchestration-history"></a>História da orquestração

O comportamento de sourcing de eventos do Quadro de Tarefas Duráveis está intimamente associado ao código de função do orquestrador que escreve. Suponha que tem uma função orquestradora de acorrentar a atividade, como a seguinte função orquestradora:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

Em cada `await` declaração (C#) ou `yield` (JavaScript/Python), o Quadro de Tarefas Durável verifica o estado de execução da função em algum backend de armazenamento durável (normalmente armazenamento da tabela Azure). Este estado é o que é referido como a história da *orquestração.*

### <a name="history-table"></a>Tabela de história

De um modo geral, o Quadro de Tarefas Duráveis faz o seguinte em cada posto de controlo:

1. Guarda o histórico de execução nas mesas de armazenamento do Azure.
2. Encosuta mensagens para funções que o orquestrador quer invocar.
3. Encosuta mensagens para o próprio &mdash; orquestrador, por exemplo, mensagens temporais duráveis.

Uma vez concluído o ponto de verificação, a função do orquestrador é livre de ser removida da memória até que haja mais trabalho para fazer.

> [!NOTE]
> O Azure Storage não oferece quaisquer garantias transacionais entre guardar dados para o armazenamento de mesas e filas. Para lidar com falhas, o fornecedor de armazenamento Funções Duradouras utiliza *padrões de consistência eventuais.* Estes padrões garantem que não se perdem dados se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, a história da função mostrada anteriormente parece algo com a seguinte tabela no Armazenamento de Mesa Azure (abreviada para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | CarimboDeDataEHora               | Entrada | Name             | Resultado                                                    | Estado |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Execução Começada      | 2017-05-05T18:45:28.852Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrquestradorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | Tarefas programadas         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorCompletado | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "Olá Tóquio!".                                        |                     |
| eaee885b | OrquestradorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | Tarefas programadas         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorCompletado | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "Olá Seattle!".                                      |                     |
| eaee885b | OrquestradorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Tarefas programadas         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrquestradorCompletado | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "Olá Londres!".                                       |                     |
| eaee885b | OrquestradorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrquestradorCompletado | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | Execução Executada    | 2017-05-05T18:45:35.044Z |       |                  | "["Olá Tóquio!"""Olá Seattle!""""Olá Londres!""]" | Concluído           |

Algumas notas sobre os valores da coluna:

* **PartitionKey**: Contém o ID de instância da orquestração.
* **EventType**: Representa o tipo de evento. Pode ser um dos seguintes tipos:
  * **OrquestraçãoStarted**: A função de orquestrador retomada a partir de um aguardado ou está a funcionar pela primeira vez. A `Timestamp` coluna é usada para povoar o valor determinístico para as `CurrentUtcDateTime` `currentUtcDateTime` APIs (.NET), (JavaScript) e `current_utc_datetime` (Python).
  * **Execução Iniciada**: A função de orquestrador começou a executar pela primeira vez. Este evento também contém a entrada de função na `Input` coluna.
  * **TaskScheded**: Foi agendada uma função de atividade. O nome da função de atividade é capturado na `Name` coluna.
  * **TaskCompleted**: Uma função de atividade concluída. O resultado da função está na `Result` coluna.
  * **Temporizado:** Foi criado um temporizador durável. A `FireAt` coluna contém a hora de UTC programada em que o temporizador expira.
  * **TimerFired**: Um temporizador durável disparou.
  * **EventRaised**: Foi enviado um evento externo para a instância de orquestração. A `Name` coluna captura o nome do evento e a coluna capta a carga útil do `Input` evento.
  * **OrchestratorCompleted**: A função de orquestrador aguardava.
  * **ContinueAsNew**: A função orquestradora completou e reiniciou-se com novo estado. A `Result` coluna contém o valor, que é usado como a entrada na instância reiniciada.
  * **ExecuçãoCompletada**: A função do orquestrador correu até ao fim (ou falhou). As saídas da função ou os detalhes de erro são armazenados na `Result` coluna.
* **Calendário :** O calendário UTC do evento histórico.
* **Nome**: O nome da função que foi invocada.
* **Entrada**: A entrada formatada por JSON da função.
* **Resultado**: A saída da função; isto é, o seu valor de retorno.

> [!WARNING]
> Embora seja útil como ferramenta de depuragem, não tenha qualquer dependência desta mesa. Pode mudar à medida que a extensão de Funções Duradouras evolui.

Sempre que a função retoma de um `await` (C#) ou `yield` (JavaScript/Python), o Quadro de Tarefas Durável reexecuta a função orquestradora do zero. Em cada repetição, consulta o histórico de execução para determinar se a operação atual de async ocorreu.  Se a operação ocorreu, o quadro repete imediatamente a saída dessa operação e passa para a seguinte `await` (C#) ou `yield` (JavaScript/Python). Este processo continua até que toda a história seja repetida. Uma vez que a história atual tenha sido repetida, as variáveis locais terão sido restauradas aos seus valores anteriores.

## <a name="features-and-patterns"></a>Características e padrões

As secções seguintes descrevem as características e padrões das funções orquestradoras.

### <a name="sub-orchestrations"></a>Sub-orquestrações

As funções orquestradoras podem chamar funções de atividade, mas também outras funções orquestradoras. Por exemplo, pode construir uma orquestração maior a partir de uma biblioteca de funções orquestradoras. Ou pode executar várias instâncias de uma função orquestradora em paralelo.

Para mais informações e, por exemplo, consulte o artigo [Sub-orquestrações.](durable-functions-sub-orchestrations.md)

### <a name="durable-timers"></a>Temporizadores duráveis

As orquestrações podem *agendar temporizadores duradouros* para implementar atrasos ou para estabelecer o tratamento de tempo limite em ações de async. Utilize temporizadores duráveis em funções de orquestrador em vez de `Thread.Sleep` e `Task.Delay` (C#), ou `setTimeout()` `setInterval()` (JavaScript), ou `time.sleep()` (Python).

Para mais informações e, por exemplo, consulte o artigo [temporizadores duráveis.](durable-functions-timers.md)

### <a name="external-events"></a>Eventos externos

As funções orquestradoras podem esperar que eventos externos atualizem uma instância de orquestração. Esta funcionalidade de Funções Duradouras é frequentemente útil para lidar com uma interação humana ou outras chamadas externas.

Para mais informações e, por exemplo, consulte o artigo [Eventos Externos.](durable-functions-external-events.md)

### <a name="error-handling"></a>Processamento de erros

As funções de orquestrador podem utilizar as funções de tratamento de erros da linguagem de programação. Padrões existentes como `try` / `catch` são suportados em código de orquestração.

As funções orquestradoras também podem adicionar políticas de repetição às funções de atividade ou sub-orquestrador que chamam. Se uma função de atividade ou sub-orquestrador falhar com uma exceção, a política de relemisão especificada pode atrasar e voltar a tentar a execução até um número especificado de vezes.

> [!NOTE]
> Se houver uma exceção não manipulada numa função orquestradora, a instância de orquestração será completada num `Failed` estado. Um caso de orquestração não pode ser novamente julgado depois de ter falhado.

Para obter mais informações e, por exemplo, consulte o artigo [de tratamento de erros.](durable-functions-error-handling.md)

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Secções críticas (Funções Duradouras 2.x, atualmente apenas .NET)

Os casos de orquestração são de linha única, por isso não é necessário preocuparmo-nos com as condições da raça *dentro* de uma orquestração. No entanto, as condições de corrida são possíveis quando as orquestrações interagem com sistemas externos. Para mitigar as condições de regata ao interagir com sistemas externos, as funções do orquestrador podem definir *secções críticas* utilizando um `LockAsync` método em .NET.

O seguinte código de amostra mostra uma função orquestradora que define uma secção crítica. Entra na secção crítica utilizando o `LockAsync` método. Este método requer a passagem de uma ou mais referências a uma [Entidade Durável,](durable-functions-entities.md)que gere duramente o estado de bloqueio. Apenas um único exemplo desta orquestração pode executar o código na secção crítica de cada vez.

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

O `LockAsync` adquire o(s) fechadura(s) durável e devolve um `IDisposable` que termina a secção crítica quando eliminado. Este `IDisposable` resultado pode ser usado juntamente com um bloco para obter uma `using` representação sintática da secção crítica. Quando uma função de orquestrador entra numa secção crítica, apenas uma instância pode executar esse bloco de código. Quaisquer outras instâncias que tentem entrar na secção crítica serão bloqueadas até que a instância anterior saia da secção crítica.

A função de secção crítica também é útil para coordenar alterações a entidades duráveis. Para obter mais informações sobre secções críticas, consulte o tema ["Coordenação de Entidades" das entidades Duráveis.](durable-functions-entities.md#entity-coordination)

> [!NOTE]
> As secções críticas estão disponíveis em Funções Duradouras 2.0 ou superiores. Atualmente, apenas as orquestrações .NET implementam esta funcionalidade.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Chamando pontos finais HTTP (Funções Duradouras 2.x)

As funções orquestradoras não estão autorizadas a fazer I/O, conforme descrito nas [restrições do código de função do orquestrador](durable-functions-code-constraints.md). A solução típica para esta limitação é embrulhar qualquer código que precise fazer I/O numa função de atividade. As orquestrações que interagem com sistemas externos usam frequentemente funções de atividade para fazer chamadas HTTP e devolver o resultado à orquestração.

# <a name="c"></a>[C#](#tab/csharp)

Para simplificar este padrão comum, as funções do orquestrador podem usar o `CallHttpAsync` método para invocar AS HTTP diretamente.

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Além de apoiar padrões básicos de pedido/resposta, o método suporta o manuseamento automático de padrões de sondagens comuns de async HTTP 202, e também suporta a autenticação com serviços externos utilizando [Identidades Geridas.](../../active-directory/managed-identities-azure-resources/overview.md)

Para obter mais informações e para exemplos detalhados, consulte o artigo [de funcionalidades HTTP.](durable-functions-http-features.md)

> [!NOTE]
> Chamar pontos finais HTTP diretamente das funções do orquestrador está disponível em Funções Duráveis 2.0 ou superiores.

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. A recomendação é passar numa série de objetos ou objetos compósitos.

# <a name="c"></a>[C#](#tab/csharp)

Em .NET também pode utilizar objetos [ValueTuple.](/dotnet/csharp/tuples) A amostra a seguir está a utilizar novas funcionalidades de [ValueTuple adicionadas](/dotnet/csharp/tuples) com [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples):

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

#### <a name="getweather-activity"></a>`GetWeather` Atividade

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Atividade

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Restrições de código do orquestrador](durable-functions-code-constraints.md)
