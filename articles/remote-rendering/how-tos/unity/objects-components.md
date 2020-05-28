---
title: Objetos e componentes do jogo de unidade
description: Descreve métodos específicos de unidade para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.openlocfilehash: 2f9f0e164f7ab0a6b146aad3a2809bf85e5aa4be
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020664"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com objetos e componentes de jogos do Unity

A renderização remota azure (ARR) está otimizada para um grande número de objetos (ver [Limitações).](../../reference/limits.md) Embora seja possível gerir grandes e complexas hierarquias no hospedeiro, replicá-las todas em Unidade em dispositivos de baixa potência é inviável.

Assim, quando um modelo é carregado no hospedeiro, a Azure Remote Rendering espelha a informação sobre a estrutura do modelo no dispositivo cliente (que incorrerá no tráfego de rede), mas não replica os objetos e componentes da Unidade. Em vez disso, espera que solicite manualmente os objetos e componentes necessários do jogo da Unidade, de modo a que possa limitar a sobrecarga ao que é realmente necessário. Desta forma, tem mais controlo sobre o desempenho do cliente.

Consequentemente, a integração de unidade da Renderização Remota Azure vem com uma funcionalidade adicional para replicar a estrutura de renderização remota a pedido.

## <a name="load-a-model-in-unity"></a>Carregue um modelo em Unidade

Quando carregas um modelo, obtém-se uma referência ao objeto raiz do modelo carregado. Esta referência não é um objeto de jogo da Unidade, mas pode transformá-lo num usando o método de extensão `Entity.GetOrCreateGameObject()` . Esta função espera um argumento de `UnityCreationMode` tipo. Se passar, o objeto de `CreateUnityComponents` jogo de Unidade recém-criado será adicionalmente preenchido com componentes proxy para todos os componentes de Renderização Remota que existem no hospedeiro. Recomenda-se, no entanto, `DoNotCreateUnityComponents` preferir, manter as despesas mínimas.

### <a name="load-model-with-task"></a>Modelo de carga com tarefa

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Modelo de carga com co-rotinas de unidade

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Modelo de carga com padrão de aguardar

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

As amostras de código acima utilizaram o modelo de carregamento via SAS porque o modelo incorporado está carregado. Dirigir-se ao modelo através de recipientes de bolhas (utilizando `LoadModelAsync` e ) funciona totalmente `LoadModelParams` análogamente.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Criar um objeto de jogo de Unidade adiciona implicitamente um `RemoteEntitySyncObject` componente ao objeto do jogo. Este componente é utilizado para sincronizar a entidade transformar-se no servidor. Por predefinição, o utilizador exige que `RemoteEntitySyncObject` o utilizador ligue explicitamente para sincronizar o estado de `SyncToRemote()` Unidade local para o servidor. A ativação `SyncEveryFrame` sincronizará o objeto automaticamente.

Os objetos com um `RemoteEntitySyncObject` podem ter os seus filhos remotos instantaneamente e mostrados no editor da Unidade através do **:::no-loc text="Show children":::** botão.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componentes de invólucro

[Os componentes ligados](../../concepts/components.md) a entidades de renderização remota são expostos à Unidade através de proxy `MonoBehavior` s. Estes proxies representam o componente remoto em Unidade, e reencaminhar todas as modificações para o hospedeiro.

Para criar componentes proxy Remote Rendering, utilize o método de `GetOrCreateArrComponent` extensão:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Vidas acopcadas

A vida de uma [entidade](../../concepts/entities.md) remota e um objeto de jogo da Unidade é acopvidado enquanto eles são ligados através de um `RemoteEntitySyncObject` . Se ligar `UnityEngine.Object.Destroy(...)` com tal objeto de jogo, a entidade remota também será removida.

Para destruir o objeto do jogo da Unidade, sem afetar a entidade remota, primeiro é preciso recorrer `Unbind()` ao `RemoteEntitySyncObject` .

O mesmo acontece com todos os componentes de procuração. Para destruir apenas a representação do lado do cliente, você precisa ligar `Unbind()` primeiro para o componente proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Próximos passos

* [Configurar o Remote Rendering para o Unity](unity-setup.md)
* [Tutorial: Trabalhar com entidades remotas na Unidade](../../tutorials/unity/working-with-remote-entities.md)
