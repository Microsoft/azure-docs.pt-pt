---
title: Visão geral do ciclo de vida do ator do Azure Service Fabric
description: Explica o ciclo de vida fiável do ator de tecido de serviço, a recolha de lixo e a eliminação manual dos atores e do seu estado
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576048"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida do ator, recolha automática de lixo e eliminação manual
Um ator é ativado na primeira vez que uma chamada é feita para qualquer um dos seus métodos. Um ator é desativado (lixo recolhido pelos atores em tempo de execução) se não for utilizado por um período de tempo configurável. Um ator e o seu estado também podem ser apagados manualmente a qualquer momento.

## <a name="actor-activation"></a>Ativação do ator
Quando um ator é ativado, ocorre o seguinte:

* Quando uma chamada vem para um ator e não se já está ativo, um novo ator é criado.
* O estado do ator está carregado se estiver a manter o estado.
* O `OnActivateAsync` método (C#) ou `onActivateAsync` (Java) (que pode ser ultrapassado na implementação do ator) é chamado.
* O ator é agora considerado ativo.

## <a name="actor-deactivation"></a>Desativação do ator
Quando um ator é desativado, ocorre o seguinte:

* Quando um ator não é usado durante algum tempo, é removido da tabela Ative Actors.
* O `OnDeactivateAsync` método (C#) ou `onDeactivateAsync` (Java) (que pode ser ultrapassado na implementação do ator) é chamado. Isto limpa todos os temporizadores para o ator. Operações de atores como mudanças de estado não devem ser chamadas deste método.

> [!TIP]
> O tempo de execução dos Fabric Actors emite alguns [eventos relacionados com a ativação e desativação do ator.](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) São úteis em diagnósticos e monitorização de desempenho.
>
>

### <a name="actor-garbage-collection"></a>Coleção de lixo do ator
Quando um ator é desativado, as referências ao objeto do ator são libertadas e pode ser recolhido normalmente pelo tempo comum de execução da linguagem (CLR) ou pela máquina virtual java (JVM) do coletor de lixo. A recolha de lixo só limpa o objeto do ator; **não** remove o estado armazenado no Diretor de Estado do ator. Da próxima vez que o ator for ativado, um novo objeto ator é criado e o seu estado é restaurado.

O que conta como "ser usado" para fins de desativação e recolha de lixo?

* Receber uma chamada
* `IRemindable.ReceiveReminderAsync` método sendo invocado (aplicável apenas se o ator usar lembretes)

> [!NOTE]
> se o ator usa temporizadores e o seu temporizador é invocado, **não** conta como "ser usado".
>
>

Antes de entrarmos nos detalhes da desativação, é importante definir os seguintes termos:

* *Intervalo de digitalização*. Este é o intervalo em que os Atores analisam a sua tabela Ative Actors para atores que podem ser desativados e recolhidos lixo. O valor predefinido para isto é de 1 minuto.
* *Tempo de 2019.* Esta é a quantidade de tempo que um ator precisa para permanecer inusitado (inativado) antes de poder ser desativado e recolher lixo. O valor predefinido para isto é de 60 minutos.

Normalmente, não é necessário alterar estes padrão. No entanto, se necessário, estes intervalos podem ser alterados ao `ActorServiceSettings` registar o seu Serviço de [Ator](service-fabric-reliable-actors-platform.md):

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
Para cada ator ativo, o tempo de execução do ator acompanha a quantidade de tempo que tem estado inativo (ou seja, não utilizado). O ator verifica cada um dos atores `ScanIntervalInSeconds` para ver se pode ser lixo recolhido e marca-o se esteve inativo para `IdleTimeoutInSeconds` .

Sempre que um ator é usado, o seu tempo de marcha lenta é reposto a 0. Depois disso, o ator só pode ser recolhido lixo se voltar a ficar inativo. `IdleTimeoutInSeconds` Lembre-se que um ator é considerado como tendo sido usado se um método de interface de ator ou um chamado de lembrete do ator for executado. Um ator **não** é considerado como tendo sido usado se o seu temporizador for executado.

O diagrama que se segue mostra o ciclo de vida de um único ator para ilustrar estes conceitos.

![Exemplo do tempo ocioso][1]

O exemplo mostra o impacto das chamadas, lembretes e temporizadores do método do ator na vida deste ator. Merecem referir os seguintes pontos sobre o exemplo:

* ScanInterval e IdleTimeout estão definidos para 5 e 10, respectivamente. (As unidades não importam aqui, uma vez que o nosso propósito é apenas ilustrar o conceito.)
* A varredura para os atores serem recolhidos acontece em T=0,5,10,15,20,25, conforme definido pelo intervalo de digitalização de 5.
* Um temporizador periódico dispara em T=4,8,12,16,20,24, e a sua chamada executa. Não afeta o tempo ocioso do ator.
* Uma chamada de método do ator no T=7 reinicia o tempo de marcha lenta para 0 e atrasa a recolha de lixo do ator.
* Um lembrete de ator executa em T=14 e atrasa ainda mais a recolha de lixo do ator.
* Durante a recolha de lixo em T=25, o tempo de inatividade do ator finalmente excede o tempo de 10, e o ator é lixo recolhido.

Um ator nunca será recolhido enquanto executa um dos seus métodos, não importa quanto tempo seja gasto na execução desse método. Como mencionado anteriormente, a execução de métodos de interface do ator e chamadas de lembrete impede a recolha de lixo, repondo o tempo de inativo do ator para 0. A execução de chamadas de temporizador não repõe o tempo de marcha lenta a 0. No entanto, a recolha de lixo do ator é adiada até que a chamada do temporizador tenha concluído a execução.

## <a name="manually-deleting-actors-and-their-state"></a>Eliminação manual dos atores e do seu estado
A recolha de lixo de atores desativados só limpa o objeto do ator, mas não remove dados que são armazenados no Diretor de Estado de um ator. Quando um ator é reativado, os seus dados são novamente disponibilizados através do Diretor de Estado. Nos casos em que os atores armazenam dados em Gerente de Estado e são desativados mas nunca reativados, pode ser necessário limpar os seus dados.  Por exemplo, como eliminar os atores, ler [apagar os atores e o seu estado.](service-fabric-reliable-actors-delete-actors.md)

## <a name="next-steps"></a>Passos seguintes
* [Temporizadores e lembretes do ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de atores](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico de ator e monitorização de desempenho](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do ator](/previous-versions/azure/dn971626(v=azure.100))
* [C# Código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
