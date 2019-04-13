---
title: Controle de versão em funções duráveis - Azure
description: Saiba como implementar o controle de versão na extensão de funções duráveis para as funções do Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 33ca6c36cd11d53a3c50a8374181c511fd2f8c3e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549089"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Controle de versão em funções duráveis (funções do Azure)

É inevitável que as funções serão adicionadas, removidas e alteradas durante o ciclo de vida de um aplicativo. [Funções duráveis](durable-functions-overview.md) permite o encadeamento funciona em conjunto, de forma a que não eram possíveis anteriormente e este encadeamento afeta a forma como pode manipular o controle de versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com alterações de última hora

Existem vários exemplos de alterações de última hora de conhecer. Este artigo aborda os mais comuns. O tema principal por trás de todos eles é que ambas as orquestrações de função de novas e existentes são afetadas por alterações ao código de função.

### <a name="changing-activity-function-signatures"></a>A alteração de assinaturas de função de atividade

Uma alteração de assinatura refere-se a uma alteração no nome, entrada ou saída de uma função. Se esse tipo de alteração é feito para uma função de atividade, ele poderá interromper a função de orquestrador que depende da mesma. Se atualizar a função de orquestrador para contemplar esta alteração, poderá interromper as instâncias em trânsito existentes.

Por exemplo, suponha que temos a função a seguir.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta função simplista recebe os resultados da **Foo** e transmite-o para **barra**. Vamos supor que temos de alterar o valor de retorno **Foo** partir `bool` para `int` para oferecer suporte a uma variedade mais ampla de valores de resultado. O resultado tem o seguinte aspeto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta alteração funciona bem para todas as novas instâncias de função de orquestrador mas divide quaisquer instâncias em trânsito. Por exemplo, considere o caso em que uma instância da orquestração chama **Foo**, recebe de volta um valor Booleano e, em seguida, pontos de verificação. Se a alteração de assinatura for implementada nesse ponto, a instância foi efetuada a verificação falhará imediatamente quando é retomada e replays a chamada para `context.CallActivityAsync<int>("Foo")`. Isto acontece porque é o resultado da tabela de histórico `bool` mas o novo código tenta desserializar em `int`.

Isso é apenas uma das muitas formas diferentes, que uma alteração de assinatura pode quebrar instâncias existentes. Em geral, se precisar de alterar a forma como um orquestrador chama uma função, em seguida, a alteração é suscetível de ser problemático.

### <a name="changing-orchestrator-logic"></a>Alterar a lógica do orchestrator

A outra classe de problemas de controle de versão provenientes de alterar o código de função de orquestrador de uma forma que confunde a lógica de repetição para instâncias em trânsito.

Considere a seguinte função do orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora vamos supor que deseja fazer uma alteração aparentemente inocente para adicionar outra chamada de função.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Esta alteração adiciona uma nova chamada de função para **SendNotification** entre **Foo** e **barra**. Não há nenhuma alteração de assinatura. O problema surge quando uma instância existente é retomado após a chamada para **barra**. Durante a reprodução, se a chamada para o original **Foo** devolvido `true`, em seguida, chamará a repetição de orchestrator **SendNotification** que não se encontra no respetivo histórico de execução. Como resultado, a estrutura de tarefa durável falha com um `NonDeterministicOrchestrationException` porque encontrou uma chamada ao **SendNotification** quando ele esperava receber uma chamada para **barra**.

## <a name="mitigation-strategies"></a>Estratégias de atenuação

Aqui estão algumas das estratégias para lidar com os desafios de controle de versão:

* Não fazer nada
* Parar todas as instâncias em trânsito
* Implementações lado a lado

### <a name="do-nothing"></a>Não fazer nada

A maneira mais fácil para lidar com uma alteração significativa é permitir que a orquestração em trânsito instâncias falharem. Novas instâncias a executar o código foi alterado com êxito.

Se se trata de um problema depende a importância das suas instâncias em trânsito. Se estiver em fase de desenvolvimento e não se preocupa em trânsito instâncias, pode ser bom o suficiente. No entanto, terá de lidar com exceções e erros no seu pipeline de diagnóstico. Se quiser evitar essas coisas, considere as outras opções de controle de versão.

### <a name="stop-all-in-flight-instances"></a>Parar todas as instâncias em trânsito

Outra opção é parar todas as instâncias em trânsito. Isso pode ser feito ao desmarcar o conteúdo do interno **controle fila** e **fila de item de trabalho** filas. As instâncias serão bloqueadas para sempre onde estão, mas não agrupará a telemetria com mensagens de falha. Isso é ideal no desenvolvimento rápido de protótipo.

> [!WARNING]
> Os detalhes destas filas podem ser alterados ao longo do tempo, pelo que não contam com essa técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implementações lado a lado

É a maneira mais à prova de falhas para garantir que as alterações interruptivas são implementadas em segurança por meio de implantação lado a lado com as versões mais antigas. Isso pode ser feito através de qualquer uma das seguintes técnicas:

* Implementar todas as atualizações como funções totalmente novas (novos nomes).
* Implemente todas as atualizações como uma nova aplicação de função com uma conta de armazenamento diferente.
* Implementar uma nova cópia da aplicação de função, mas com um atualizado `TaskHub` nome. Essa é a técnica de recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do hub de tarefa

O hub de tarefa pode ser configurado no *Host. JSON* ficheiros da seguinte forma:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Funções 2.x

O valor predefinido é `DurableFunctionsHub`.

Todas as entidades de armazenamento do Azure são nomeadas com base no `HubName` valor de configuração. Ao dar o hub de tarefa um novo nome, garante que separado de filas e tabelas de histórico, são criadas para a nova versão do seu aplicativo.

Recomendamos que implemente a nova versão da aplicação de função para um novo [bloco de implementação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Blocos de implementação permitem-lhe executar várias cópias da sua função aplicação lado a lado com apenas um deles como o Active Directory *produção* ranhura. Quando estiver pronto para expor a nova lógica de orquestração para a sua infraestrutura existente, pode ser tão simples quanto a troca da nova versão para o bloco de produção.

> [!NOTE]
> Esta estratégia funciona melhor quando usa os acionadores de HTTP e webhook das funções do orchestrator. Para acionadores de não-HTTP, tais como filas ou Hubs de eventos, a definição de Acionador deve [derivam de uma definição de aplicação](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) que é atualizado como parte da operação de troca.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com o desempenho e problemas de dimensionamento](durable-functions-perf-and-scale.md)
