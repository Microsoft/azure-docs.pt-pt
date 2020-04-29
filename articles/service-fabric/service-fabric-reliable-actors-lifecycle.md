---
title: Visão geral do ciclo de vida do ator de serviço Azure
description: Explica o ciclo de vida do ator fiável do tecido de serviço, a recolha de lixo e a desafoga manualmente os atores e o seu estado
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79258320"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida do ator, recolha automática de lixo e eliminação manual
Um ator é ativado na primeira vez que uma chamada é feita para qualquer um dos seus métodos. Um ator é desativado (lixo recolhido pelos Atores) se não for utilizado durante um período de tempo configurável. Um ator e o seu estado também podem ser apagados manualmente a qualquer momento.

## <a name="actor-activation"></a>Ativação de ator
Quando um ator é ativado, ocorre o seguinte:

* Quando uma chamada chega para um ator e uma já não está ativa, é criado um novo ator.
* O estado do ator está carregado se mantiver o estado.
* O `OnActivateAsync` método (C#) ou `onActivateAsync` (Java) (que pode ser ultrapassado na implementação do ator) é chamado.
* O ator é agora considerado ativo.

## <a name="actor-deactivation"></a>Desativação do ator
Quando um ator é desativado, ocorre o seguinte:

* Quando um ator não é usado durante algum tempo, é removido da mesa Ative Actors.
* O `OnDeactivateAsync` método (C#) ou `onDeactivateAsync` (Java) (que pode ser ultrapassado na implementação do ator) é chamado. Isto limpa todos os tempos para o ator. Operações de ator como mudanças de estado não devem ser chamadas a partir deste método.

> [!TIP]
> O tempo de execução dos Atores de Tecido emite alguns [eventos relacionados com a ativação e desativação](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)do ator. São úteis em diagnósticos e monitorização de desempenho.
>
>

### <a name="actor-garbage-collection"></a>Coleção de lixo do ator
Quando um ator é desativado, as referências ao objeto do ator são libertadas e pode ser lixo recolhido normalmente pelo tempo de execução da linguagem comum (CLR) ou pelo colecionador de lixo da máquina virtual java (JVM). A recolha de lixo só limpa o objeto do ator; **não** remove o Estado armazenado no Gerente de Estado do ator. Da próxima vez que o ator for ativado, um novo objeto ator é criado e o seu estado é restaurado.

O que conta como "ser usado" com o propósito de desativação e recolha de lixo?

* Recebendo uma chamada
* `IRemindable.ReceiveReminderAsync`método invocado (aplicável apenas se o ator usar lembretes)

> [!NOTE]
> se o ator usa temporizadores e o seu temporizador é invocado, **não** conta como "ser usado".
>
>

Antes de entrarmos nos detalhes da desativação, é importante definir os seguintes termos:

* *Intervalo de digitalização.* Este é o intervalo em que os Atores analisam a sua tabela Ative Actors para atores que podem ser desativados e lixo recolhido. O valor padrão para isto é de 1 minuto.
* *Tempo limite.* Este é o tempo que um ator precisa para permanecer não utilizado (inativo) antes de poder ser desativado e recolher lixo. O valor padrão para isto é de 60 minutos.

Normalmente, não é necessário alterar estes incumprimentos. No entanto, se necessário, estes `ActorServiceSettings` intervalos podem ser alterados ao registar o seu Serviço de [Ator:](service-fabric-reliable-actors-platform.md)

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
Para cada ator ativo, o tempo de execução do ator acompanha o tempo que tem estado inativo (isto é, não utilizado). O ator verifica cada um `ScanIntervalInSeconds` dos atores para ver se pode ser lixo recolhido `IdleTimeoutInSeconds`e marca-o se tem estado inativo para .

Sempre que um ator é usado, o seu tempo de inatividade é reposto para 0. Depois disso, o ator só pode ser recolhido `IdleTimeoutInSeconds`lixo se voltar a ficar inativo para . Recorde-se que um ator é considerado como tendo sido usado se um método de interface de ator ou um callback de lembrete de ator for executado. Um ator **não** é considerado como tendo sido usado se o seu temporizador é executado.

O diagrama seguinte mostra o ciclo de vida de um único ator para ilustrar estes conceitos.

![Exemplo de tempo inativo][1]

O exemplo mostra o impacto das chamadas, lembretes e tempos de ator na vida deste ator. Merecem ser mencionados os seguintes pontos sobre o exemplo:

* ScanInterval e IdleTimeout estão definidos para 5 e 10, respectivamente. (As unidades não importam aqui, uma vez que o nosso propósito é apenas ilustrar o conceito.)
* A varredura para os atores recolhidos acontece em T=0,5,10,15,20,25, conforme definido pelo intervalo de digitalização de 5.
* Um temporizador periódico dispara em T=4,8,12,16,20,24, e o seu callback executa. Não afeta o tempo inativo do ator.
* Uma chamada de método do ator no T=7 repõe o tempo inativo para 0 e atrasa a recolha de lixo do ator.
* Um callback de lembrete de ator executa em T=14 e atrasa ainda mais a recolha de lixo do ator.
* Durante a recolha de lixo em T=25, o tempo inativo do ator finalmente excede o tempo inativo de 10, e o ator é lixo recolhido.

Um ator nunca será recolhido enquanto executa um dos seus métodos, não importa quanto tempo seja gasto na execução desse método. Como mencionado anteriormente, a execução de métodos de interface de ator e chamadas de lembrete impede a recolha de lixo, repondo o tempo inativo do ator para 0. A execução de chamadas temporais não repõe o tempo inativo para 0. No entanto, a recolha de lixo do ator é adiada até que o chamador temporizador tenha terminado a execução.

## <a name="manually-deleting-actors-and-their-state"></a>Apagar manualmente os atores e o seu estado
A recolha de lixo de atores desativados só limpa o objeto do ator, mas não remove dados que são armazenados no State Manager de um ator. Quando um ator é reativado, os seus dados são novamente disponibilizados através do State Manager. Nos casos em que os atores armazenam dados em State Manager e são desativados mas nunca reativados, pode ser necessário limpar os seus dados.  Por exemplo, como eliminar os atores, ler [eliminar atores e o seu estado.](service-fabric-reliable-actors-delete-actors.md)

## <a name="next-steps"></a>Passos seguintes
* [Tempors de ator e lembretes](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentração do ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Código da amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código da amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
