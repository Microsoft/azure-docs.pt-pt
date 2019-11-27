---
title: Orquestrações duráveis-Azure Functions
description: Introdução ao recurso de orquestração do Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 54e1eb0be18de8e5ed420e96629d6f23473272fe
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74545715"
---
# <a name="durable-orchestrations"></a>Orquestrações duráveis

Durable Functions é uma extensão de [Azure Functions](../functions-overview.md). Você pode usar uma *função de orquestrador* para orquestrar a execução de outras funções duráveis dentro de um aplicativo de funções. As funções de orquestrador têm as seguintes características:

* As funções de orquestrador definem fluxos de trabalho de função usando código de procedimento. Não são necessários esquemas ou designers declarativos.
* As funções de orquestrador podem chamar outras funções duráveis de forma síncrona e assíncrona. A saída de funções chamadas pode ser salva de forma confiável em variáveis locais.
* As funções de orquestrador são duráveis e confiáveis. O andamento da execução é automaticamente marcado quando a função "Await" ou "produz". O estado local nunca é perdido quando o processo é reciclado ou a VM é reinicializada.
* As funções de orquestrador podem ser de execução longa. O ciclo de vida total de uma *instância de orquestração* pode ser de segundos, dias, meses ou nunca terminando.

Este artigo fornece uma visão geral das funções de orquestrador e como elas podem ajudá-lo a resolver vários desafios de desenvolvimento de aplicativos. Se você ainda não estiver familiarizado com os tipos de funções disponíveis em um aplicativo Durable Functions, leia primeiro o artigo sobre [tipos de função durável](durable-functions-types-features-overview.md) .

## <a name="orchestration-identity"></a>Identidade de orquestração

Cada *instância* de uma orquestração tem um identificador de instância (também conhecido como uma *ID de instância*). Por padrão, cada ID de instância é um GUID gerado automaticamente. No entanto, as IDs de instância também podem ser qualquer valor de cadeia de caracteres gerado pelo usuário. Cada ID de instância de orquestração deve ser exclusiva dentro de um [Hub de tarefas](durable-functions-task-hubs.md).

A seguir estão algumas regras sobre IDs de instância:

* As IDs de instância devem ter entre 1 e 256 caracteres.
* As IDs de instância não devem começar com `@`.
* As IDs de instância não devem conter `/`, `\`, `#`ou `?` caracteres.
* As IDs de instância não devem conter caracteres de controle.

> [!NOTE]
> Geralmente, é recomendável usar IDs de instância gerada automaticamente sempre que possível. As IDs de instância geradas pelo usuário são destinadas a cenários em que há um mapeamento de um-para-um entre uma instância de orquestração e alguma entidade externa específica do aplicativo, como uma ordem de compra ou um documento.

A ID da instância de uma orquestração é um parâmetro necessário para a maioria das [operações de gerenciamento de instância](durable-functions-instance-management.md). Eles também são importantes para diagnósticos, como [a pesquisa por meio de dados de controle de orquestração](durable-functions-diagnostics.md#application-insights) em Application insights para fins de solução de problemas ou análise. Por esse motivo, é recomendável salvar IDs de instância geradas em algum local externo (por exemplo, um banco de dados ou em logs de aplicativo), onde elas podem ser facilmente referenciadas mais tarde.

## <a name="reliability"></a>Fiabilidade

As funções de orquestrador mantêm seu estado de execução de forma confiável usando o padrão de design de [fornecimento de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Em vez de armazenar diretamente o estado atual de uma orquestração, a estrutura de tarefa durável usa um repositório somente de acréscimo para registrar a série completa de ações que a orquestração de função usa. Um armazenamento somente de acréscimo tem muitos benefícios em comparação ao "despejo" do estado de tempo de execução completo. Os benefícios incluem maior desempenho, escalabilidade e capacidade de resposta. Você também obtém consistência eventual para dados transacionais e histórico e trilhas de auditoria completas. As trilhas de auditoria dão suporte a ações de compensação confiáveis.

Durable Functions usa o fornecimento de eventos de forma transparente. Nos bastidores, o operador `await`C#() ou `yield` (JavaScript) em uma função de orquestrador resulta no controle do thread do Orchestrator de volta para o Dispatcher do Framework de tarefa durável. Em seguida, o Dispatcher confirma as novas ações que a função de orquestrador agendou (como chamar uma ou mais funções filhas ou agendar um temporizador durável) para o armazenamento. A ação de confirmação transparente acrescenta ao histórico de execução da instância de orquestração. O histórico é armazenado em uma tabela de armazenamento. Em seguida, a ação de confirmação adiciona mensagens a uma fila para agendar o trabalho real. Neste ponto, a função de orquestrador pode ser descarregada da memória.

Quando uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador ativa e executa novamente toda a função do início para recompilar o estado local. Durante a repetição, se o código tentar chamar uma função (ou qualquer outro trabalho assíncrono), o Framework de tarefa durável consultará o histórico de execução da orquestração atual. Se descobrir que a [função de atividade](durable-functions-types-features-overview.md#activity-functions) já foi executada e gerou um resultado, ela repetirá o resultado dessa função e o código do orquestrador continuará a ser executado. A repetição continua até que o código da função seja concluído ou até que o novo trabalho assíncrono seja agendado.

> [!NOTE]
> Para que o padrão de reprodução funcione corretamente e de forma confiável, o código de função do orquestrador deve ser *determinístico*. Para obter mais informações sobre restrições de código para funções de orquestrador, consulte o tópico [restrições de código de função de orquestrador](durable-functions-code-constraints.md) .

> [!NOTE]
> Se uma função de orquestrador emitir mensagens de log, o comportamento de reprodução poderá fazer com que mensagens de log duplicadas sejam emitidas. Consulte o tópico [log](durable-functions-diagnostics.md#logging) para saber mais sobre por que esse comportamento ocorre e como contorná-lo.

## <a name="orchestration-history"></a>Histórico de orquestração

O comportamento de fornecimento de eventos do Framework de tarefa durável está intimamente acoplado ao código da função de orquestrador que você escreve. Suponha que você tenha uma função de orquestrador de encadeamento de atividades, como C# a seguinte função de orquestrador:

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

Se você estiver codificando em JavaScript, sua função de orquestrador de encadeamento de atividades poderá ser parecida com o seguinte código de exemplo:

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

Em cada instrução `await`C#() ou `yield` (JavaScript), a estrutura de tarefa durável verifica o estado de execução da função em algum back-end de armazenamento durável (normalmente, armazenamento de tabelas do Azure). Esse estado é o que é conhecido como o *histórico de orquestração*.

### <a name="history-table"></a>Tabela de histórico

Em termos gerais, o Framework de tarefa durável faz o seguinte em cada ponto de verificação:

1. Salva o histórico de execução em tabelas do armazenamento do Azure.
2. Enfileira mensagens para funções que o orquestrador deseja invocar.
3. Enfileira mensagens para o orquestrador em si &mdash; por exemplo, mensagens de temporizador duráveis.

Após a conclusão do ponto de verificação, a função de orquestrador é livre para ser removida da memória até que haja mais trabalho a ser feito.

> [!NOTE]
> O armazenamento do Azure não fornece nenhuma garantia transacional entre salvar dados em filas e armazenamento de tabelas. Para lidar com falhas, o provedor de armazenamento Durable Functions usa padrões de *consistência eventual* . Esses padrões garantem que nenhum dado seja perdido se houver uma falha ou perda de conectividade no meio de um ponto de verificação.

Após a conclusão, o histórico da função mostrado anteriormente é semelhante à tabela a seguir no armazenamento de tabelas do Azure (abreviado para fins de ilustração):

| PartitionKey (InstanceId)                     | EventType             | Carimbo de data/hora               | Input | Nome             | Resultado                                                    | Estado |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | nulo  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" Hello Tokyo! "" "                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" Olá, Seattle! ""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" Hello London! "" "                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Olá Tokyo!" "," "Olá, Seattle!" "," "Olá, Londres!" "]" | Concluído           |

Algumas observações sobre os valores da coluna:

* **PartitionKey**: contém a ID da instância da orquestração.
* **EventType**: representa o tipo do evento. Pode ser um dos seguintes tipos:
  * **OrchestrationStarted**: a função de orquestrador retomou-se de um Await ou está em execução pela primeira vez. A coluna `Timestamp` é usada para preencher o valor determinístico para as APIs de `CurrentUtcDateTime` (.NET) e `currentUtcDateTime` (JavaScript).
  * **ExecutionStarted**: a função de orquestrador começou a ser executada pela primeira vez. Esse evento também contém a entrada de função na coluna `Input`.
  * **TaskScheduled**: uma função de atividade foi agendada. O nome da função de atividade é capturado na coluna `Name`.
  * **TaskCompleted**: uma função de atividade foi concluída. O resultado da função está na coluna `Result`.
  * **TimerCreated**: um temporizador durável foi criado. A coluna `FireAt` contém a hora UTC agendada na qual o temporizador expira.
  * **TimerFired**: um temporizador durável foi acionado.
  * **EventRaised**: um evento externo foi enviado para a instância de orquestração. A coluna `Name` captura o nome do evento e a coluna `Input` captura a carga do evento.
  * **OrchestratorCompleted**: a função de orquestrador esperada.
  * **ContinueAsNew**: a função de orquestrador foi concluída e reiniciada com o novo estado. A coluna `Result` contém o valor, que é usado como entrada na instância reiniciada.
  * **ExecutionCompleted**: a função de orquestrador foi executada até a conclusão (ou falhou). As saídas da função ou os detalhes do erro são armazenados na coluna `Result`.
* **Timestamp**: o carimbo de data/hora UTC do evento de histórico.
* **Name**: o nome da função que foi invocada.
* **Input**: a entrada formatada em JSON da função.
* **Resultado**: a saída da função; ou seja, seu valor de retorno.

> [!WARNING]
> Embora seja útil como uma ferramenta de depuração, não assuma nenhuma dependência nessa tabela. Ele pode mudar à medida que a extensão de Durable Functions evolui.

Sempre que a função é retomada de umC#`await` () ou `yield` (JavaScript), a estrutura de tarefa durável executa novamente a função de orquestrador do zero. Em cada nova execução, ele consulta o histórico de execução para determinar se a operação assíncrona atual foi realizada.  Se a operação tiver ocorrido, a estrutura repetirá a saída dessa operação imediatamente e passará para a próxima `await` (C#) ou `yield` (JavaScript). Esse processo continua até que todo o histórico tenha sido reproduzido. Depois que o histórico atual for reproduzido, as variáveis locais serão restauradas para seus valores anteriores.

## <a name="features-and-patterns"></a>Recursos e padrões

As seções a seguir descrevem os recursos e padrões das funções de orquestrador.

### <a name="sub-orchestrations"></a>Sub-orquestrações

As funções de orquestrador podem chamar funções de atividade, mas também outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior a partir de uma biblioteca de funções de orquestrador. Ou, você pode executar várias instâncias de uma função de orquestrador em paralelo.

Para obter mais informações e exemplos, consulte o artigo [sub-orquestrações](durable-functions-sub-orchestrations.md) .

### <a name="durable-timers"></a>Temporizadores duráveis

As orquestrações podem agendar *temporizadores duráveis* para implementar atrasos ou para configurar o tratamento de tempo limite em ações assíncronas. Use temporizadores duráveis em funções de orquestrador em vez de `Thread.Sleep`C#e `Task.Delay` () ou `setTimeout()` e `setInterval()` (JavaScript).

Para obter mais informações e exemplos, consulte o artigo de [temporizadores duráveis](durable-functions-timers.md) .

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem aguardar eventos externos para atualizar uma instância de orquestração. Esse Durable Functions recurso geralmente é útil para lidar com uma interação humana ou com outros retornos de chamada externos.

Para obter mais informações e exemplos, consulte o artigo [eventos externos](durable-functions-external-events.md) .

### <a name="error-handling"></a>Processamento de erros

As funções de orquestrador podem usar os recursos de tratamento de erros da linguagem de programação. Padrões existentes como `try`/`catch` têm suporte no código de orquestração.

As funções de orquestrador também podem adicionar políticas de repetição para as funções de atividade ou de suborquestrador que chamam. Se uma função de atividade ou de suborquestrador falhar com uma exceção, a política de repetição especificada poderá atrasar e repetir a execução automaticamente até um número de vezes especificado.

> [!NOTE]
> Se houver uma exceção sem tratamento em uma função de orquestrador, a instância de orquestração será concluída em um estado de `Failed`. Uma instância de orquestração não pode ser repetida após a falha.

Para obter mais informações e exemplos, consulte o artigo [tratamento de erros](durable-functions-error-handling.md) .

### <a name="critical-sections-durable-functions-2x"></a>Seções críticas (Durable Functions 2. x)

As instâncias de orquestração são de thread único, portanto, não é necessário se preocupar com as condições *de corrida em* uma orquestração. No entanto, as condições de corrida são possíveis quando as orquestrações interagem com sistemas externos. Para reduzir as condições de corrida ao interagir com sistemas externos, as funções de orquestrador podem definir *seções críticas* usando um método `LockAsync` no .net.

O código de exemplo a seguir mostra uma função de orquestrador que define uma seção crítica. Ele entra na seção crítica usando o método `LockAsync`. Esse método requer a passagem de uma ou mais referências a uma [entidade durável](durable-functions-entities.md), que permanentemente gerencia o estado de bloqueio. Somente uma única instância dessa orquestração pode executar o código na seção crítica de cada vez.

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

O `LockAsync` adquire os bloqueios duráveis e retorna um `IDisposable` que termina a seção crítica quando descartado. Esse `IDisposable` resultado pode ser usado junto com um bloco de `using` para obter uma representação sintática da seção crítica. Quando uma função de orquestrador entra em uma seção crítica, apenas uma instância pode executar esse bloco de código. Quaisquer outras instâncias que tentarem entrar na seção crítica serão bloqueadas até que a instância anterior saia da seção crítica.

O recurso de seção crítica também é útil para coordenar alterações em entidades duráveis. Para obter mais informações sobre seções críticas, consulte o tópico ["coordenação de entidade" de entidades duráveis](durable-functions-entities.md#entity-coordination) .

> [!NOTE]
> As seções críticas estão disponíveis no Durable Functions 2,0 e superior. Atualmente, somente as orquestrações do .NET implementam esse recurso.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Chamando pontos de extremidade HTTP (Durable Functions 2. x)

As funções de orquestrador não são permitidas para fazer e/s, conforme descrito em [restrições de código de função de orquestrador](durable-functions-code-constraints.md). A solução alternativa típica para essa limitação é encapsular qualquer código que precise fazer e/s em uma função de atividade. Orquestrações que interagem com sistemas externos frequentemente usam funções de atividade para fazer chamadas HTTP e retornar o resultado para a orquestração.

Para simplificar esse padrão comum, as funções de orquestrador podem usar o método `CallHttpAsync` no .NET para invocar APIs HTTP diretamente. Além de oferecer suporte aos padrões básicos de solicitação/resposta, o `CallHttpAsync` dá suporte à manipulação automática de padrões de sondagem HTTP 202 assíncronos comuns e também dá suporte à autenticação com serviços externos usando [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md).

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

Para obter mais informações e exemplos detalhados, consulte o artigo [recursos http](durable-functions-http-features.md) .

> [!NOTE]
> Chamar pontos de extremidade HTTP diretamente de funções de orquestrador está disponível no Durable Functions 2,0 e superior.

### <a name="passing-multiple-parameters"></a>Passando vários parâmetros

Não é possível passar vários parâmetros para uma função de atividade diretamente. A recomendação é passar uma matriz de objetos ou usar objetos [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) no .net.

O exemplo a seguir está usando novos recursos do [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) adicionados com [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Restrições de código do Orchestrator](durable-functions-code-constraints.md)
