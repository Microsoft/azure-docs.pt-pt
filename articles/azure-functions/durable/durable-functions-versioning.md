---
title: Versão em Funções Duradouras - Azure
description: Saiba como implementar a versão na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74232762"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versão em Funções Duradouras (Funções Azure)

É inevitável que as funções sejam adicionadas, removidas e alteradas ao longo da vida de uma aplicação. [As Funções Duradouras](durable-functions-overview.md) permitem acorrentar funções em conjunto de formas que não eram anteriormente possíveis, e esta corrente afeta a forma como pode lidar com a versão.

## <a name="how-to-handle-breaking-changes"></a>Como lidar com mudanças de rutura

Há vários exemplos de mudanças de rutura a ter em conta. Este artigo discute os mais comuns. O tema principal por trás de todos eles é que as orquestrações de funções novas e existentes são impactadas por alterações ao código de função.

### <a name="changing-activity-or-entity-function-signatures"></a>Alteração de assinaturas de funções de atividade ou de entidade

Uma alteração de assinatura refere-se a uma alteração no nome, entrada ou saída de uma função. Se este tipo de alteração for feita a uma função de atividade ou entidade, pode quebrar qualquer função orquestradora que dependa dela. Se atualizar a função do orquestrador para acomodar esta alteração, poderá quebrar as instâncias existentes no voo.

Como exemplo, suponha que temos a seguinte função de orquestrador.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Esta função simplista leva os resultados de **Foo** e passa-o para **o Bar.** Vamos supor que precisamos mudar o valor de retorno do **Foo** `bool` de para suportar uma maior variedade de `int` valores de resultados. O resultado tem o seguinte aspeto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Os exemplos C# anteriores visam as Funções Duráveis 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

Esta mudança funciona bem para todos os novos casos da função orquestradora, mas quebra quaisquer instâncias de voo. Por exemplo, considere o caso em que uma instância de orquestração chama uma função `Foo` chamada, recebe de volta um valor boolean, e, em seguida, pontos de verificação. Se a alteração da assinatura for implantada neste momento, a instância de verificação falhará imediatamente quando retomar e reproduzir a chamada para `context.CallActivityAsync<int>("Foo")` . Esta falha acontece porque o resultado na tabela de história `bool` é, mas o novo código tenta desseecializá-lo em `int` .

Este exemplo é apenas uma de muitas maneiras diferentes de que uma mudança de assinatura pode quebrar os casos existentes. Em geral, se um orquestrador precisa de mudar a forma como chama uma função, então a mudança é provável que seja problemática.

### <a name="changing-orchestrator-logic"></a>Mudança na lógica do orquestrador

A outra classe de problemas de versão vem da alteração do código de função do orquestrador de uma forma que confunde a lógica de repetição para instâncias de voo.

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
> Os exemplos C# anteriores visam as Funções Duráveis 2.x. Para funções duradouras 1.x, deve utilizar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext` . Para obter mais informações sobre as diferenças entre versões, consulte o artigo [das versões Funções Duradouras.](durable-functions-versions.md)

Esta alteração adiciona uma nova chamada de função para **SendNotification** entre **Foo** e **Bar**. Não há alterações de assinatura. O problema surge quando um caso existente retoma da chamada para o **Bar.** Durante a repetição, se a chamada original para **Foo** voltou `true` , então a repetição do orquestrador chamará para **SendNotification**, que não está na sua história de execução. Como resultado, o Quadro de Tarefas Duráveis falha com um `NonDeterministicOrchestrationException` porque encontrou uma chamada para a **SendNotification** quando esperava ver uma chamada para **o Bar**. O mesmo tipo de problema pode ocorrer quando se adicionam chamadas a APIs "duráveis", `CreateTimer` `WaitForExternalEvent` incluindo, etc.

## <a name="mitigation-strategies"></a>Estratégias de mitigação

Eis algumas das estratégias para lidar com os desafios da versão:

* Não fazer nada
* Pare todos os casos de voo
* Implantações lado a lado

### <a name="do-nothing"></a>Não fazer nada

A maneira mais fácil de lidar com uma mudança de rutura é deixar que as instâncias de orquestração a bordo falhem. Novas instâncias executam com sucesso o código alterado.

Se este tipo de falha é um problema depende da importância dos seus casos de voo. Se estiver em desenvolvimento ativo e não se importar com casos de voo, isto pode ser bom o suficiente. No entanto, terá de lidar com exceções e erros no seu pipeline de diagnóstico. Se quiser evitar essas coisas, considere as outras opções de versão.

### <a name="stop-all-in-flight-instances"></a>Pare todos os casos de voo

Outra opção é parar todas as instâncias de voo. Parar todas as instâncias pode ser feito limpando o conteúdo das filas internas **de controlo e** fila de **trabalho.** Os casos ficarão para sempre presos onde estão, mas não irão baralhar os seus registos com mensagens de falha. Esta abordagem é ideal no desenvolvimento rápido do protótipo.

> [!WARNING]
> Os detalhes destas filas podem mudar ao longo do tempo, por isso não confie nesta técnica para cargas de trabalho de produção.

### <a name="side-by-side-deployments"></a>Implantações lado a lado

A forma mais à prova de falhas de garantir que as alterações são implementadas com segurança é implantando-as lado a lado com as suas versões mais antigas. Isto pode ser feito utilizando qualquer uma das seguintes técnicas:

* Implemente todas as atualizações como funções inteiramente novas, deixando as funções existentes como está. Isto pode ser complicado porque os chamadores das novas versões de função devem ser atualizados também seguindo as mesmas diretrizes.
* Implemente todas as atualizações como uma nova aplicação de função com uma conta de armazenamento diferente.
* Implemente uma nova cópia da aplicação de função com a mesma conta de armazenamento, mas com um `taskHub` nome atualizado. As implementações lado a lado são a técnica recomendada.

### <a name="how-to-change-task-hub-name"></a>Como alterar o nome do centro de tarefas

O centro de tarefas pode ser configurado no *host.jsem* arquivo da seguinte forma:

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

O valor predefinido para Funções Duradouras v1.x é `DurableFunctionsHub` . A partir de Funções Duradouras v2.0, o nome do hub de tarefa padrão é o mesmo que o nome da aplicação de função em Azure, ou `TestHubName` se correr fora de Azure.

Todas as entidades de Armazenamento Azure são nomeadas com base no valor de `hubName` configuração. Ao dar ao centro de tarefas um novo nome, certifique-se de que são criadas filas separadas e tabela de história para a nova versão da sua aplicação. A aplicação de função, no entanto, deixará de processar eventos para orquestrações ou entidades criadas sob o nome do centro de tarefas anterior.

Recomendamos que implemente a nova versão da aplicação de função para uma nova [Ranhura de Implementação.](../functions-deployment-slots.md) As ranhuras de implementação permitem executar várias cópias da sua aplicação de função lado a lado com apenas uma delas como a ranhura de *produção* ativa. Quando estiver pronto para expor a nova lógica de orquestração à sua infraestrutura existente, pode ser tão simples como trocar a nova versão pela ranhura de produção.

> [!NOTE]
> Esta estratégia funciona melhor quando utiliza os gatilhos HTTP e webhook para funções de orquestradores. Para os gatilhos não-HTTP, tais como filas ou Centros de Eventos, a definição de gatilho deve [derivar de uma definição de aplicação](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) que seja atualizada como parte da operação de troca.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba como lidar com questões de desempenho e escala](durable-functions-perf-and-scale.md)
