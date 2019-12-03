---
title: Visão geral do ciclo de vida do ator do Azure Service Fabric | Microsoft Docs
description: Explica Service Fabric ciclo de vida de ator confiável, coleta de lixo e exclusão manual de atores e seu estado
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 1a8e95c634a1d30b7c566fcd907cb06f34043fa9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706483"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida do ator, coleta de lixo automática e exclusão manual
Um ator é ativado na primeira vez que uma chamada é feita a qualquer um de seus métodos. Um ator é desativado (lixo coletado pelo tempo de execução dos atores) se ele não for usado por um período de tempo configurável. Um ator e seu estado também podem ser excluídos manualmente a qualquer momento.

## <a name="actor-activation"></a>Ativação de ator
Quando um ator é ativado, ocorre o seguinte:

* Quando uma chamada chega a um ator e uma já não está ativa, um novo ator é criado.
* O estado do ator será carregado se ele estiver mantendo o estado.
* O método `OnActivateAsync`C#() ou `onActivateAsync` (Java) (que pode ser substituído na implementação do ator) é chamado.
* O ator agora é considerado ativo.

## <a name="actor-deactivation"></a>Desativação de ator
Quando um ator é desativado, ocorre o seguinte:

* Quando um ator não é usado por algum período de tempo, ele é removido da tabela de atores ativos.
* O método `OnDeactivateAsync`C#() ou `onDeactivateAsync` (Java) (que pode ser substituído na implementação do ator) é chamado. Isso limpa todos os timers do ator. As operações de ator, como alterações de estado, não devem ser chamadas a partir desse método.

> [!TIP]
> O tempo de execução dos atores de malha emite alguns [eventos relacionados à ativação e à desativação de ator](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Eles são úteis no diagnóstico e no monitoramento de desempenho.
>
>

### <a name="actor-garbage-collection"></a>Coleta de lixo do ator
Quando um ator é desativado, as referências ao objeto de ator são liberadas e podem ser coletadas com lixo normalmente pelo coletor de lixo Common Language Runtime (CLR) ou Java Virtual Machine (JVM). A coleta de lixo limpa apenas o objeto de ator; Ele não **Remove o** estado armazenado no Gerenciador de estado do ator. Na próxima vez que o ator for ativado, um novo objeto de ator será criado e seu estado será restaurado.

O que conta como "sendo usado" para fins de desativação e coleta de lixo?

* Recebendo uma chamada
* `IRemindable.ReceiveReminderAsync` método que está sendo invocado (aplicável somente se o ator usar lembretes)

> [!NOTE]
> Se o ator usar temporizadores e seu retorno de chamada de temporizador for invocado, ele **não contará** como "sendo usado".
>
>

Antes de entrarmos nos detalhes da desativação, é importante definir os seguintes termos:

* *Intervalo de verificação*. Esse é o intervalo no qual o tempo de execução dos atores examina sua tabela de atores ativos para atores que podem ser desativados e coletados como lixo. O valor padrão para isso é 1 minuto.
* *Tempo limite de ociosidade*. Esse é o período de tempo que um ator precisa permanecer não utilizado (ocioso) antes que possa ser desativado e coletado pelo lixo. O valor padrão para isso é de 60 minutos.

Normalmente, você não precisa alterar esses padrões. No entanto, se necessário, esses intervalos podem ser alterados por meio de `ActorServiceSettings` ao registrar seu [serviço de ator](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Para cada ator ativo, o tempo de execução do ator controla a quantidade de tempo em que ele esteve ocioso (ou seja, não usado). O tempo de execução de ator verifica cada um dos atores a cada `ScanIntervalInSeconds` para ver se ele pode ser coletado por lixo e o marca se ele esteve ocioso por `IdleTimeoutInSeconds`.

Sempre que um ator é usado, seu tempo ocioso é redefinido como 0. Depois disso, o ator poderá ser lixo coletado somente se ele continuar inativo por `IdleTimeoutInSeconds`. Lembre-se de que um ator é considerado como sendo usado se um método de interface de ator ou um retorno de chamada de lembrete de ator for executado. Um ator **não** será considerado para ser usado se o retorno de chamada do temporizador for executado.

O diagrama a seguir mostra o ciclo de vida de um único ator para ilustrar esses conceitos.

![Exemplo de tempo ocioso][1]

O exemplo mostra o impacto de chamadas de método de ator, lembretes e temporizadores no tempo de vida desse ator. Vale a pena mencionar os seguintes pontos sobre o exemplo:

* ScanInterval e IdleTimeout são definidos como 5 e 10, respectivamente. (As unidades não importam aqui, já que nossa finalidade é apenas ilustrar o conceito.)
* A verificação dos atores a serem coletados pelo lixo ocorre em T = 0, 5, 10, 15, 20, 25, conforme definido pelo intervalo de verificação de 5.
* Um temporizador periódico é acionado em T = 4, 8, 12, 16, 20, 24 e seu retorno de chamada é executado. Ele não afeta o tempo ocioso do ator.
* Uma chamada de método de ator em T = 7 redefine o tempo ocioso como 0 e atrasa a coleta de lixo do ator.
* Um retorno de chamada de lembrete de ator é executado em T = 14 e atrasa ainda mais a coleta de lixo do ator.
* Durante a verificação de coleta de lixo em T = 25, o tempo ocioso do ator finalmente excede o tempo limite de ociosidade de 10 e o ator é coletado pelo lixo.

Um ator nunca será coletado pelo lixo enquanto estiver executando um de seus métodos, não importa quanto tempo é gasto na execução desse método. Como mencionado anteriormente, a execução de métodos de interface de ator e retornos de chamada de lembrete impede a coleta de lixo redefinindo o tempo ocioso do ator como 0. A execução de retornos de chamada do temporizador não redefine o tempo ocioso para 0. No entanto, a coleta de lixo do ator é adiada até que o retorno de chamada do temporizador tenha concluído a execução.

## <a name="manually-deleting-actors-and-their-state"></a>Excluindo manualmente atores e seu estado
A coleta de lixo de atores desativados limpa apenas o objeto de ator, mas não remove os dados armazenados no Gerenciador de estado de um ator. Quando um ator é reativado, seus dados são novamente disponibilizados para ele por meio do Gerenciador de estado. Nos casos em que os atores armazenam dados no Gerenciador de estado e são desativados, mas nunca foram ativados novamente, pode ser necessário limpar seus dados.  Para obter exemplos de como excluir atores, leia [excluir atores e seu estado](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Passos seguintes
* [Temporizadores e lembretes de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#Código de exemplo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
