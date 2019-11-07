---
title: Controle de versão no Durable Functions-Azure
description: Saiba como implementar o controle de versão na extensão de Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4b4e82acbd3037c70b87731c0661605041090435
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614515"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controle de versão em Durable Functions (Azure Functions)

É inevitável que as funções serão adicionadas, removidas e alteradas durante o tempo de vida de um aplicativo. [Durable Functions](durable-functions-overview.md) permite encadear funções de maneiras que antes não eram possíveis, e esse encadeamento afeta como você pode lidar com o controle de versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com alterações significativas

Há vários exemplos de alterações significativas a serem consideradas. Este artigo aborda as mais comuns. O tema principal por trás de todos eles é que as orquestrações de função novas e existentes são afetadas por alterações no código de função.

### <a name="changing-activity-or-entity-function-signatures"></a>Alterando assinaturas de atividade ou função de entidade

Uma alteração de assinatura refere-se a uma alteração no nome, na entrada ou na saída de uma função. Se esse tipo de alteração for feito em uma função de atividade ou entidade, ele poderá interromper qualquer função de orquestrador que dependa dela. Se você atualizar a função de orquestrador para acomodar essa alteração, poderá interromper as instâncias em andamento existentes.

Por exemplo, suponha que tenhamos a seguinte função de orquestrador.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Essa função simples usa os resultados de **foo** e as transmite para a **barra**. Vamos supor que precisamos alterar o valor de retorno de **foo** de `bool` para `int` para dar suporte a uma variedade maior de valores de resultado. O resultado é semelhante a este:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Os exemplos C# anteriores têm como alvo Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

Essa alteração funciona bem para todas as novas instâncias da função de orquestrador, mas interrompe todas as instâncias em andamento. Por exemplo, considere o caso em que uma instância de orquestração chama uma função chamada `Foo`, retorna um valor booliano e, em seguida, pontos de verificação. Se a alteração de assinatura for implantada neste ponto, a instância de ponto de verificação falhará imediatamente quando for retomada e repetirá a chamada para `context.CallActivityAsync<int>("Foo")`. Essa falha ocorre porque o resultado na tabela de histórico é `bool`, mas o novo código tenta desserializá-la em `int`.

Este exemplo é apenas uma das várias maneiras diferentes pelas quais uma alteração de assinatura pode interromper instâncias existentes. Em geral, se um orquestrador precisar alterar a maneira como ele chama uma função, a alteração provavelmente será problemática.

### <a name="changing-orchestrator-logic"></a>Alterando a lógica do orquestrador

A outra classe de problemas de controle de versão vem da alteração do código de função de orquestrador de forma a confundir a lógica de reprodução para instâncias em andamento.

Considere a seguinte função de orquestrador:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora vamos supor que você deseja fazer uma alteração aparentemente inocente para adicionar outra chamada de função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Os exemplos C# anteriores têm como alvo Durable Functions 2. x. Para Durable Functions 1. x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo [Durable Functions versões](durable-functions-versions.md) .

Essa alteração adiciona uma nova chamada de função a **SendNotification** entre **foo** e **bar**. Não há alterações de assinatura. O problema ocorre quando uma instância existente é retomada da chamada para **bar**. Durante a repetição, se a chamada original para **foo** retornou `true`, a reprodução do orquestrador chamará **SendNotification**, que não está em seu histórico de execução. Como resultado, o Framework de tarefa durável falha com um `NonDeterministicOrchestrationException` porque ele encontrou uma chamada para **SendNotification** quando esperava ver uma chamada para **bar**. O mesmo tipo de problema pode ocorrer ao adicionar qualquer chamada a APIs "duráveis", incluindo `CreateTimer`, `WaitForExternalEvent`, etc.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Aqui estão algumas das estratégias para lidar com os desafios de controle de versão:

* Não fazer nada
* Parar todas as instâncias em andamento
* Implantações lado a lado

### <a name="do-nothing"></a>Não fazer nada

A maneira mais fácil de lidar com uma alteração significativa é deixar as instâncias de orquestração em andamento falharem. Novas instâncias executaram com êxito o código alterado.

Se esse tipo de falha é um problema, depende da importância de suas instâncias em andamento. Se você estiver em desenvolvimento ativo e não se importa com instâncias em andamento, isso pode ser bom o suficiente. No entanto, você precisará lidar com exceções e erros em seu pipeline de diagnóstico. Se você quiser evitar essas coisas, considere as outras opções de controle de versão.

### <a name="stop-all-in-flight-instances"></a>Parar todas as instâncias em andamento

Outra opção é interromper todas as instâncias em andamento. A interrupção de todas as instâncias pode ser feita limpando o conteúdo das filas de fila de **controle** interno e de filas **de trabalho** . As instâncias ficarão paralisadas para sempre onde estiverem, mas elas não obstruirão seus logs com mensagens de falha. Essa abordagem é ideal no desenvolvimento rápido de protótipos.

> [!WARNING]
> Os detalhes dessas filas podem mudar ao longo do tempo, portanto, não confie nessa técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implantações lado a lado

A maneira mais reprovada de falhas para garantir que as alterações significativas sejam implantadas com segurança é implantá-las lado a lado com suas versões mais antigas. Isso pode ser feito usando qualquer uma das seguintes técnicas:

* Implante todas as atualizações como funções totalmente novas, deixando as funções existentes como estão. Isso pode ser complicado porque os chamadores das novas versões de função devem ser atualizados e seguir as mesmas diretrizes.
* Implante todas as atualizações como um novo aplicativo de funções com uma conta de armazenamento diferente.
* Implante uma nova cópia do aplicativo de funções com a mesma conta de armazenamento, mas com um nome de `taskHub` atualizado. Implantações lado a lado é a técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do hub de tarefas

O Hub de tarefas pode ser configurado no arquivo *host. JSON* da seguinte maneira:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funções 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

O valor padrão para Durable Functions v1. x é `DurableFunctionsHub`. A partir do Durable Functions v 2.0, o nome do hub de tarefas padrão é o mesmo que o nome do aplicativo de funções no Azure, ou `TestHubName` se estiver sendo executado fora do Azure.

Todas as entidades de armazenamento do Azure são nomeadas com base no valor de configuração `hubName`. Ao dar um novo nome ao Hub de tarefas, você garante que filas e tabelas de histórico separadas sejam criadas para a nova versão do seu aplicativo. O aplicativo de funções, no entanto, interromperá o processamento de eventos para orquestrações ou entidades criadas sob o nome do hub de tarefas anterior.

Recomendamos que você implante a nova versão do aplicativo de funções em um novo [slot de implantação](../functions-deployment-slots.md). Os slots de implantação permitem que você execute várias cópias do seu aplicativo de funções lado a lado com apenas uma delas como o slot de *produção* ativo. Quando você estiver pronto para expor a nova lógica de orquestração para sua infraestrutura existente, ela pode ser tão simples quanto trocar a nova versão no slot de produção.

> [!NOTE]
> Essa estratégia funciona melhor quando você usa gatilhos HTTP e webhook para funções de orquestrador. Para gatilhos não HTTP, como filas ou hubs de eventos, a definição do gatilho deve [derivar de uma configuração de aplicativo](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) que é atualizada como parte da operação de permuta.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com problemas de desempenho e escala](durable-functions-perf-and-scale.md)
