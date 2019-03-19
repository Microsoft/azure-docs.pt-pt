---
title: Descrição geral do ciclo de vida de atores do Azure Service Fabric | Documentos da Microsoft
description: Explica o Service Fabric Reliable Actor ciclo de vida, coleta de lixo e eliminar manualmente os atores e o respetivo estado
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 10f78b3b78e90fbb4f1d50cf581bfbce263f44aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872726"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Ciclo de vida de ator, coleta de lixo automática e manual delete
Um ator é ativado pela primeira vez que é feita uma chamada para qualquer um dos seus métodos. Um ator é desativado (lixo coletado pelo tempo de execução de Atores) se não for utilizado por um período configurável de tempo. Um ator e seu estado também podem ser eliminadas manualmente em qualquer altura.

## <a name="actor-activation"></a>Ativação de ator
Quando um ator é ativado, ocorre o seguinte:

* Quando vem de uma chamada para um ator e já não estiver ativo, é criado um novo ator.
* Estado do ator é carregado se ele mantém o estado.
* O `OnActivateAsync` (c#) ou `onActivateAsync` (Java) método (que pode ser substituído na implementação de ator) é chamado.
* O ator seja considerado Active Directory.

## <a name="actor-deactivation"></a>Desativação de ator
Quando um ator é desativado, ocorre o seguinte:

* Quando um ator não for utilizado durante um determinado período de tempo, esta é removida da tabela de Atores do Active Directory.
* O `OnDeactivateAsync` (c#) ou `onDeactivateAsync` (Java) método (que pode ser substituído na implementação de ator) é chamado. Isso limpa todos os temporizadores para o ator. Operações de ator, como o estado de alterações não devem ser chamadas a partir desse método.

> [!TIP]
> O tempo de execução de Atores de recursos de infraestrutura emite algumas [eventos relacionados à ativação de ator e a desativação](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Eles são úteis no diagnóstico e monitorização de desempenho.
>
>

### <a name="actor-garbage-collection"></a>Coleta de lixo de ator
Quando um ator é desativado, referências ao objeto de ator são lançadas e pode ser lixo coletado normalmente pelo coletor de lixo do java (JVM) de máquina de virtual ou tempo de execução comum para a linguagem (CLR). Coleta de lixo limpa apenas o objeto de ator; ele faz **não** remover estado armazenado no Gestor de estado do ator. Na próxima vez que o ator é ativado, é criado um novo objeto de ator e respetivo estado é restaurado.

O que conta como "a ser utilizado" com o objetivo de Desativação e a coleta de lixo?

* Receber uma chamada
* `IRemindable.ReceiveReminderAsync` método sendo invocado (aplicável apenas se o ator utiliza lembretes)

> [!NOTE]
> Se o ator usa temporizadores e seu retorno de chamada do timer é invocado, ele faz **não** contagem como "a ser utilizado".
>
>

Antes de entrarmos em detalhes sobre a desativação, é importante definir as condições seguintes:

* *Intervalo de análise*. Este é o intervalo a que o tempo de execução de Atores analisa a sua tabela de Atores do Active Directory para atores que podem ser desativados e coletadas como lixo. O valor predefinido para isso é de 1 minuto.
* *Tempo limite de inatividade*. Esta é a quantidade de tempo que um ator tem de permanecer não utilizadas (inativo) antes de ele pode ser desativado e coletadas como lixo. O valor padrão é 60 minutos.

Normalmente, não é necessário alterar esses padrões. No entanto, se necessário, estes intervalos podem ser alterados através de `ActorServiceSettings` ao registar as suas [serviço de Ator](service-fabric-reliable-actors-platform.md):

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
Para cada ator Active Directory, o runtime de ator mantém o controle da quantidade de tempo que esteve inativo (ou seja, não utilizado). O runtime de ator verifica cada um dos atores cada `ScanIntervalInSeconds` para ver se ele pode ser lixo coletado e recolhe-lo se tiver estado inativo para `IdleTimeoutInSeconds`.

Sempre que um ator é utilizado, o tempo de inatividade é reposto a 0. Depois disso, o ator pode ser lixo coletado apenas se novamente permanece inativo para `IdleTimeoutInSeconds`. Lembre-se de que um ator é considerado como terem sido utilizadas se um método de interface de ator ou um retorno de chamada do lembrete de ator for executado. É um ator **não** consideradas para terem sido utilizadas se seu retorno de chamada do timer for executado.

O diagrama seguinte mostra o ciclo de vida de um ator único para ilustrar esses conceitos.

![Exemplo de tempo de inatividade][1]

O exemplo mostra o impacto de chamadas de método de ator, lembretes e temporizadores na vida útil deste ator. Os seguintes pontos sobre o exemplo valem a pena mencionar:

* ScanInterval e IdleTimeout estão definidos como 5 e 10, respetivamente. (Unidades não interessa aqui, uma vez que o nosso objetivo é apenas ilustrar o conceito.)
* A análise de atores são coletados pelo lixo ocorre no T = 0, 5, 10, 15, 20, 25, conforme definido pelo intervalo de análise de 5.
* Um temporizador periódico é acionado em T = 4, 8, 12, 16, 20, 24, e executa o seu retorno de chamada. Ela não afeta o tempo de inatividade de ator.
* Uma chamada de método de ator em T = 7 repõe o tempo de inatividade para 0 e atrasa a coleta de lixo de ator.
* Um retorno de chamada do lembrete de ator é executada às T = 14 e ainda mais atrasa a coleta de lixo de ator.
* Durante a análise de coleta de lixo em T = 25, tempo de inatividade do ator finalmente excede o tempo limite de inatividade de 10 e o ator é coletado pelo lixo.

Um ator nunca serão lixo coletado durante sua execução um de seus métodos, não importa quanto tempo é gasto na execução desse método. Como mencionado anteriormente, a execução de métodos de interface de ator e retornos de chamada de lembrete impede a coleta de lixo ao repor o tempo de inatividade do ator para 0. A execução dos retornos de chamada do timer não repor o tempo de inatividade para 0. No entanto, a coleta de lixo de ator é adiada até que o retorno de chamada do temporizador seja concluída a execução.

## <a name="manually-deleting-actors-and-their-state"></a>Eliminar manualmente os atores e o respetivo estado
Coleta de lixo de atores desativados limpa apenas o objeto de ator, mas não remove os dados armazenados no Gestor de estado de um ator. Quando um ator é voltar a ativar, seus dados é novamente disponibilizados ao mesmo através do Gestor de estado. Nos casos onde atores armazenar dados no Gestor de estado e estiverem desativados, mas nunca é novamente ativados, pode ser necessário limpar os seus dados.  Para obter exemplos de como eliminar atores, leia [eliminar atores e o respetivo estado](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Passos Seguintes
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrada de ator](service-fabric-reliable-actors-reentrancy.md)
* [Monitorização de desempenho e diagnóstico de ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API de ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo de c#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
