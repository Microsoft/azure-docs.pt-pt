---
title: Versão em Funções Duráveis - Azure
description: Saiba implementar a versão na extensão Funções Duráveis para funções azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74232762"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versão em Funções Duráveis (Funções Azure)

É inevitável que as funções sejam adicionadas, removidas e alteradas ao longo da vida de uma aplicação. [As Funções Duráveis](durable-functions-overview.md) permitem funções de corrente em conjunto de formas que não eram anteriormente possíveis, e esta corrente afeta a forma como consegue lidar com a versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com as alterações de rutura

Há vários exemplos de alterações de rutura a ter em conta. Este artigo discute os mais comuns. O tema principal por trás de todos eles é que as orquestrações de funções novas e existentes são impactadas por alterações ao código de função.

### <a name="changing-activity-or-entity-function-signatures"></a>Alteração de assinaturas de função de atividade ou entidade

Uma alteração de assinatura refere-se a uma alteração no nome, entrada ou saída de uma função. Se este tipo de mudança for feita para uma função de atividade ou entidade, pode quebrar qualquer função orquestradora que dependa dela. Se atualizar a função de orquestrador para acomodar esta mudança, poderá quebrar as instâncias existentes no voo.

Como exemplo, suponha que temos a seguinte função orquestradora.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta função simplista pega nos resultados de **Foo** e passa-os para **bar**. Vamos supor que precisamos mudar o `bool` valor `int` de retorno de **Foo** de para apoiar uma maior variedade de valores de resultados. O resultado é o seguinte:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Os exemplos c# anteriores visam funções duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

Esta mudança funciona bem para todos os novos casos da função orquestradora, mas quebra quaisquer casos de voo. Por exemplo, considere o caso em que `Foo`uma instância de orquestração chama uma função chamada, recebe de volta um valor booleano, e depois postos de controlo. Se a alteração de assinatura for implementada neste momento, a instância de controlo falhará `context.CallActivityAsync<int>("Foo")`imediatamente quando retomar e reproduzir a chamada para . Esta falha acontece porque o resultado `bool` na tabela de história é, `int`mas o novo código tenta desserializá-lo em .

Este exemplo é apenas uma de muitas maneiras diferentes de uma mudança de assinatura pode quebrar os casos existentes. Em geral, se um orquestrador precisa de mudar a forma como chama uma função, então a mudança é suscetível de ser problemática.

### <a name="changing-orchestrator-logic"></a>Mudança da lógica do orquestrador

A outra classe de problemas de versão vem da alteração do código de função orquestrador de uma forma que confunde a lógica de repetição para casos de voo.

Considere a seguinte função orquestradora:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Agora vamos supor que quer fazer uma mudança aparentemente inocente para adicionar outra chamada de função.

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
> Os exemplos c# anteriores visam funções duráveis 2.x. Para funções duráveis 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre versões, consulte o artigo de [versões De Funções Duráveis.](durable-functions-versions.md)

Esta alteração adiciona uma nova chamada de função para **Enviar Notificação** entre **Foo** e **Bar**. Não há alterações de assinatura. O problema surge quando uma instância existente retoma da chamada para a **Bar**. Durante a repetição, se a `true`chamada original para **Foo** retornou, então a repetição do orquestrador irá chamar para **SendNotification**, que não está no seu histórico de execução. Como resultado, o Quadro de Tarefas Duráveis falha com um `NonDeterministicOrchestrationException` porque encontrou uma chamada para enviar **notificação** quando esperava ver uma chamada para **bar**. O mesmo tipo de problema pode ocorrer ao adicionar chamadas `CreateTimer` `WaitForExternalEvent`a APIs "duráveis", incluindo, etc.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Eis algumas das estratégias para lidar com desafios de versão:

* Não faça nada.
* Parar todos os casos de voo
* Implantações lado a lado

### <a name="do-nothing"></a>Não faça nada.

A maneira mais fácil de lidar com uma mudança de rutura é deixar que os casos de orquestração a bordo falhem. Novos casos executam com sucesso o código alterado.

Se este tipo de falha é um problema depende da importância dos seus casos de voo. Se está em desenvolvimento ativo e não se importa com casos de voo, isto pode ser bom o suficiente. No entanto, terá de lidar com exceções e erros no seu pipeline de diagnóstico. Se quiser evitar essas coisas, considere as outras opções de versão.

### <a name="stop-all-in-flight-instances"></a>Parar todos os casos de voo

Outra opção é parar todos os casos de voo. A paragem de todas as ocorrências pode ser feita limpando o conteúdo das filas internas de **controlo-fila** e de **fila de trabalho.** Os casos ficarão para sempre presos onde estão, mas não irão atrapalhar os seus registos com mensagens de falha. Esta abordagem é ideal no rápido desenvolvimento do protótipo.

> [!WARNING]
> Os detalhes destas filas podem mudar ao longo do tempo, por isso não confie nesta técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implantações lado a lado

A forma mais fail-proof de garantir que as alterações de rutura são implementadas com segurança é implantando-as lado a lado com as suas versões mais antigas. Isto pode ser feito usando qualquer uma das seguintes técnicas:

* Implemente todas as atualizações como funções inteiramente novas, deixando as funções existentes como estão. Isto pode ser complicado porque os chamadores das novas versões de funções devem ser atualizados, bem como seguindo as mesmas orientações.
* Implemente todas as atualizações como uma nova aplicação de funções com uma conta de armazenamento diferente.
* Implemente uma nova cópia da aplicação de funções com a mesma conta de armazenamento, mas com um nome atualizado. `taskHub` As implantações lado a lado são a técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do centro de tarefas

O centro de tarefas pode ser configurado no ficheiro *host.json* da seguinte forma:

#### <a name="functions-1x"></a>Funções 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funções 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

O valor predefinido para Funções `DurableFunctionsHub`Duráveis v1.x é . A partir de Funções Duráveis v2.0, o nome do centro de tarefas predefinido é o mesmo que o nome da aplicação de função em Azure, ou `TestHubName` se estiver a funcionar fora do Azure.

Todas as entidades de Armazenamento `hubName` Azure são nomeadas com base no valor de configuração. Ao dar ao centro de tarefas um novo nome, certifique-se de que são criadas filas separadas e tabela de história para a nova versão da sua aplicação. A aplicação de funções, no entanto, deixará de processar eventos para orquestrações ou entidades criadas sob o nome anterior do centro de tarefas.

Recomendamos que implemente a nova versão da aplicação de funções para uma nova Ranhura de [Implementação](../functions-deployment-slots.md). As ranhuras de *implementação* permitem-lhe executar várias cópias da sua aplicação de função lado a lado com apenas uma delas como a ranhura de produção ativa. Quando estiver pronto para expor a nova lógica de orquestração à sua infraestrutura existente, pode ser tão simples como trocar a nova versão pela ranhura de produção.

> [!NOTE]
> Esta estratégia funciona melhor quando utiliza o HTTP e os gatilhos do webhook para funções de orquestradores. Para os gatilhos não HTTP, tais como filas ou Centros de Eventos, a definição de gatilho deve [derivar de uma definição de aplicação](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) que é atualizada como parte da operação de swap.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com problemas de desempenho e escala](durable-functions-perf-and-scale.md)
