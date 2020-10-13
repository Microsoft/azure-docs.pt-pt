---
title: Objetos e componentes de jogo de unidade
description: Descreve métodos específicos de unidade para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019050"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com objetos e componentes de jogos do Unity

A renderização remota Azure (ARR) está otimizada para um grande número de objetos (ver [Limitações).](../../reference/limits.md) Embora seja possível gerir hierarquias grandes e complexas no hospedeiro, replicá-las todas em Unidade em dispositivos de baixa potência é inviável.

Assim, quando um modelo é carregado no hospedeiro, a Renderização Remota Azure espelha a informação sobre a estrutura do modelo no dispositivo cliente (que incorrerá no tráfego da rede), mas não replica os objetos e componentes em Unidade. Em vez disso, espera que solicites os objetos e componentes necessários do jogo Da Unidade manualmente, de modo a que possas limitar a sobrecarga ao que é realmente necessário. Desta forma, tens mais controlo sobre o desempenho do lado do cliente.

Consequentemente, a integração de Unidade da Renderização Remota Azure vem com funcionalidade adicional para replicar a estrutura de renderização remota a pedido.

## <a name="load-a-model-in-unity"></a>Carregue um modelo na Unidade

Quando carrega um modelo, obtém-se uma referência ao objeto de raiz do modelo carregado. Esta referência não é um objeto de jogo unidade, mas pode transformá-lo em um usando o método de extensão `Entity.GetOrCreateGameObject()` . Esta função espera um argumento do `UnityCreationMode` tipo. Se `CreateUnityComponents` passares, o objeto de jogo Unity recém-criado será adicionalmente preenchido com componentes proxy para todos os componentes de Renderização Remota que existam no hospedeiro. Recomenda-se, no entanto, que se `DoNotCreateUnityComponents` mantenha o mínimo.

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

### <a name="load-model-with-unity-coroutines"></a>Modelo de carga com coroutinas de unidade

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

### <a name="load-model-with-await-pattern"></a>Modelo de carga com padrão de espera

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

As amostras de código acima utilizaram o caminho de carregamento do modelo via SAS porque o modelo incorporado está carregado. Dirigir-se ao modelo através de recipientes blob (utilizando `LoadModelAsync` e ) funciona de forma totalmente `LoadModelParams` análoga.

## <a name="remoteentitysyncobject"></a>Entidade RemotaSyncObject

Criar um objeto de jogo Unidade adiciona implicitamente um `RemoteEntitySyncObject` componente ao objeto do jogo. Este componente é utilizado para sincronizar a entidade que se transforma no servidor. Por `RemoteEntitySyncObject` predefinição, o utilizador chama explicitamente `SyncToRemote()` para sincronizar o estado de Unidade local para o servidor. A ativação `SyncEveryFrame` sincronizará o objeto automaticamente.

Os objetos com uma `RemoteEntitySyncObject` lata podem ter os seus filhos remotos instantâneos e mostrados no editor da Unidade através do **:::no-loc text="Show children":::** botão.

![Entidade RemotaSyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componentes de invólucro

[Os componentes ligados](../../concepts/components.md) às entidades de renderização remota estão expostos à Unidade através de procuração `MonoBehavior` s. Estes proxies representam o componente remoto em Unidade, e encaminham todas as modificações para o hospedeiro.

Para criar componentes de renderização remota proxy, utilize o método de `GetOrCreateArrComponent` extensão:

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Vidas acopladas

A vida útil de uma [entidade](../../concepts/entities.md) remota e de um objeto de jogo da Unidade é acoplada enquanto estão ligadas através de um `RemoteEntitySyncObject` . Se ligar `UnityEngine.Object.Destroy(...)` com um objeto de jogo, a entidade remota também será removida.

Para destruir o objeto do jogo Unidade, sem afetar a entidade remota, primeiro tens de recorrer `Unbind()` ao `RemoteEntitySyncObject` .

O mesmo se aplica a todos os componentes de procuração. Para destruir apenas a representação do lado do cliente, você precisa ligar para `Unbind()` o componente proxy primeiro:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Passos seguintes

* [Configurar o Remote Rendering para o Unity](unity-setup.md)
* [Tutorial: Manipular entidades remotas na Unidade](../../tutorials/unity/manipulate-models/manipulate-models.md)
