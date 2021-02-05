---
title: Objetos e componentes de jogo de unidade
description: Descreve métodos específicos de unidade para trabalhar com entidades e componentes de renderização remota.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594151"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagir com objetos e componentes de jogos do Unity

A renderização remota Azure (ARR) está otimizada para um grande número de objetos (ver [Limitações).](../../reference/limits.md) Embora seja possível gerir hierarquias grandes e complexas no hospedeiro, replicá-las todas em Unidade em dispositivos de baixa potência é inviável.

Assim, quando um modelo é carregado no hospedeiro, a Renderização Remota Azure espelha a informação sobre a estrutura do modelo no dispositivo cliente (que incorrerá no tráfego da rede), mas não replica os objetos e componentes em Unidade. Em vez disso, espera que solicites os objetos e componentes necessários do jogo Da Unidade manualmente, de modo a que possas limitar a sobrecarga ao que é realmente necessário. Desta forma, tens mais controlo sobre o desempenho do lado do cliente.

Consequentemente, a integração de Unidade da Renderização Remota Azure vem com funcionalidade adicional para replicar a estrutura de renderização remota a pedido.

## <a name="load-a-model-in-unity"></a>Carregue um modelo na Unidade

Quando carrega um modelo, obtém-se uma referência ao objeto de raiz do modelo carregado. Esta referência não é um objeto de jogo unidade, mas pode transformá-lo em um usando o método de extensão `Entity.GetOrCreateGameObject()` . Esta função espera um argumento do `UnityCreationMode` tipo. Se `CreateUnityComponents` passares, o objeto de jogo Unity recém-criado será adicionalmente preenchido com componentes proxy para todos os componentes de Renderização Remota que existam no hospedeiro. Recomenda-se, no entanto, que se `DoNotCreateUnityComponents` mantenha o mínimo.

### <a name="load-model-with-unity-coroutines"></a>Modelo de carga com coroutinas de unidade

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Modelo de carga com padrão de espera

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

As amostras de código acima utilizaram o caminho de carregamento do modelo via SAS porque o modelo incorporado está carregado. Dirigir-se ao modelo através de recipientes blob (utilizando `LoadModelAsync` e ) funciona de forma totalmente `LoadModelOptions` análoga.

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
