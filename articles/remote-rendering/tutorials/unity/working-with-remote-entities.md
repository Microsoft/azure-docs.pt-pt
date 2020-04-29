---
title: Trabalhar com entidades remotas no Unity
description: Tutorial que mostra como trabalhar com entidades ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/01/2020
ms.topic: tutorial
ms.openlocfilehash: db1f6a53121e05b29f7e3441af027985a141bc2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81310199"
---
# <a name="tutorial-working-with-remote-entities-in-unity"></a>Tutorial: Trabalhar com entidades remotas na Unidade

[Tutorial: A criação de um projeto de Unidade do zero](project-setup.md) mostrou como configurar um novo projeto de Unidade para trabalhar com a Renderização Remota Azure. Neste tutorial, temos uma olhada na funcionalidade mais comum que cada utilizador de ARR necessita.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Escolha objetos usando moldes de raios.
> * Sobrepor estados de objetos como cor de tonalidade, estado de seleção e visibilidade.
> * Eliminar entidades remotas.
> * Mova entidades remotas ao redor.
> * Use aviões cortados para olhar para dentro de objetos.

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se em cima do [Tutorial: Criação de um projeto de Unidade do zero.](project-setup.md)

> [!TIP]
> O [repositório](https://github.com/Azure/azure-remote-rendering) de amostras ARR contém projetos de unidade preparados para todos os tutoriais na pasta *Unidade,* que você pode usar como referência.

## <a name="pick-objects"></a>Escolha objetos

Queremos interagir com objetos, por isso a primeira coisa que precisamos é de apanhar objetos debaixo do cursor do rato.

Crie um [novo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) chamado **RemoteRaycaster** e substitua todo o seu conteúdo pelo código abaixo:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var entity = await RemoteRayCast(origin, direction);
        if (entity != null)
        {
            Debug.Log("Object Hit: " + entity.Name);
        }
    }
}
```

Adicione este componente ao objeto *RemoteRendering* na sua cena.

> [!WARNING]
>
> O componente *RemoteRaycaster* requer que um componente *ARRServiceUnity* seja ligado ao mesmo objeto. *ARRServiceUnity* é uma classe de ajudante para aceder a algumas funcionalidades DE ARR mais facilmente. No entanto, só pode haver uma única instância deste componente na cena. Portanto, certifique-se de adicionar todos os componentes que requerem *ARRServiceUnity* ao mesmo GameObject.
> Se pretender aceder à funcionalidade ARR a partir de vários objetos de jogo, adicione o componente *ARRServiceUnity* apenas a um deles e refira-se ao dos outros scripts, ou aceda diretamente à funcionalidade ARR.

Pressione o reprodução, ligue-se a uma sessão e carregue um modelo. Agora aponte para os objetos na cena e veja a saída da consola. Deve imprimir o nome do objeto de cada parte que paira.

## <a name="highlight-objects"></a>Destacar objetos

Como próximo passo, queremos dar feedback visual, que partes de um modelo o utilizador está a apontar. Para isso, anexamos um [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) à entidade que escolhemos. Este componente pode ser utilizado para ativar ou desativar várias funcionalidades num objeto. Aqui a usamos para definir uma cor de tonalidade e ativar [a renderização do contorno.](../../overview/features/outlines.md)

Crie outro ficheiro de script chamado **RemoteModelEntity** e substitua o seu conteúdo pelo seguinte código:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

public class RemoteModelEntity : MonoBehaviour
{
    public Color HighlightColor = new Color(1.0f, 0.0f, 0.0f, 0.1f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;

    private RemoteEntitySyncObject localSyncObject = null;
    private ARRHierarchicalStateOverrideComponent localStateOverride = null;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();
        localStateOverride = GetComponent<ARRHierarchicalStateOverrideComponent>();

        if (localStateOverride == null)
        {
            localStateOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
            var remoteStateOverride = localSyncObject.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

            if (remoteStateOverride == null)
            {
                // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                localStateOverride.Create(RemoteManagerUnity.CurrentSession);
            }
            else
            {
                // otherwise, bind our local stateOverride component to the remote component
                localStateOverride.Bind(remoteStateOverride);
            }
        }

        localStateOverride.RemoteComponent.TintColor = HighlightColor.toRemote();
    }

    public void OnDisable()
    {
        SetStateOverride(false);

        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.Enabled = false;
            localStateOverride.enabled = false;
        }
    }

    private void SetStateOverride(bool on)
    {
        if (localStateOverride.IsComponentValid)
        {
            localStateOverride.RemoteComponent.UseTintColorState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.SelectedState = on ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);
    }
}
```
> [!CAUTION]
> Não designe este script a qualquer objeto de jogo, uma vez que será atribuído programáticamente pelo código abaixo.

Em seguida, temos que estender o nosso *RemoteRaycaster* para adicionar o componente *RemoteModelEntity* ao objeto que acabamos de escolher.

Adicione o seguinte código à implementação **do RemoteRaycaster** e remova as funções duplicadas:

```csharp
    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        focusedModel = null;
    }
```

Execute o seu projeto e aponte para um modelo, você deve vê-lo recebendo uma tonalidade vermelha e um contorno de seleção branco.

## <a name="isolate-the-selected-object"></a>Isolar o objeto selecionado

Outro uso do [HierarchicalStateOverrideComponent](../../overview/features/override-hierarchical-state.md) é a capacidade de sobrepor a visibilidade. Isto permite isolar um objeto selecionado do resto do modelo. Abra o script **RemoteModelEntity,** adicione o seguinte código e remova as funções duplicadas:

```csharp
    private bool isolated = false;
    private HierarchicalStateOverrideComponent parentOverride = null;

    public void ToggleIsolate()
    {
        SetIsolated(!isolated);
    }

    public void SetIsolated(bool on)
    {
        if (localStateOverride == null || !localStateOverride.IsComponentValid || isolated == on)
        {
            return;
        }

        // find the top most parent object that has a HierarchicalStateOverrideComponent
        if (parentOverride == null)
        {
            var modelRoot = transform;

            while (modelRoot.parent != null)
            {
                modelRoot = modelRoot.parent;

                var parentSyncObject = modelRoot.GetComponent<RemoteEntitySyncObject>();

                var stateOverrideComp = parentSyncObject?.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                if (stateOverrideComp != null)
                {
                    parentOverride = stateOverrideComp;
                }
            }
        }

        if (parentOverride != null)
        {
            isolated = on;

            parentOverride.HiddenState = isolated ? HierarchicalEnableState.ForceOn : HierarchicalEnableState.InheritFromParent;
            localStateOverride.RemoteComponent.HiddenState = isolated ? HierarchicalEnableState.ForceOff : HierarchicalEnableState.InheritFromParent;
        }
    }

    public void SetFocus(bool on)
    {
        SetStateOverride(on);

        if (!on)
        {
            SetIsolated(false);
        }
    }
```

Este código baseia-se em ter um componente de sobreposição de estado no objeto mais alto da hierarquia, o que torna todos os objetos invisíveis. Em seguida, sobrepõe-se à visibilidade novamente no objeto selecionado, para tornar visível esse objeto. Por isso, precisamos de criar um componente de sobreposição de estado no objeto raiz.

Abra o script **RemoteRendering** e insira o código abaixo na parte superior da função *LoadModel:*

```csharp
    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();
        arrService.CurrentActiveSession.Actions.CreateComponent(ObjectType.HierarchicalStateOverrideComponent, modelEntity);

        ...
    }
```

Finalmente, precisamos de uma forma de alternar a visibilidade. Abra o script **RemoteRaycaster** e substitua a função *Atualização:*

```csharp
    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        var ray = Camera.main.ScreenPointToRay(Input.mousePosition);

        Raycast(ray.origin, ray.direction);

        if (Input.GetMouseButtonDown(1) && focusedModel != null)
        {
            focusedModel.ToggleIsolate();
        }
    }
```

Executar o código e clicar à direita numa parte do modelo. O resto do modelo desaparecerá e apenas a peça em destaque permanecerá visível.

## <a name="remove-gameobject-instances-of-remote-entities"></a>Remover casos gameObject de entidades remotas

Podes ter reparado que o código continua a criar objetos, mas nunca os limpa. Isto também é visível no painel da hierarquia do objeto. Ao expandir a hierarquia de objetos remotos durante a simulação, pode ver cada vez mais objetos aparecendo cada vez que paira sobre uma nova parte do modelo.

Ter muitos objetos numa cena afeta negativamente o desempenho. Deve sempre limpar objetos que já não são necessários.

Insira o código abaixo no script **RemoteRaycaster** e remova as funções duplicadas:

```csharp
    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }
```

## <a name="move-objects"></a>Mover objetos

Como próximo passo, queremos mover um objeto selecionado. No script **RemoteRaycaster,** insira este código e remova a função duplicada:

```csharp
    private Vector3 lastPosition = Vector3.zero;

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        if (Input.GetMouseButton(0))
        {
            if (focusedModel)
            {
                // note: mousePosition is in 2D screen-space coordinates and has no relation with
                // the 3D object position. This just happens to work good enough for demonstration.
                var delta = Input.mousePosition - lastPosition;
                focusedModel.transform.position += delta * Time.deltaTime;
            }
        }
        else
        {
            Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
            var ray = Camera.main.ScreenPointToRay(position);

            Raycast(ray.origin, ray.direction);

            if (Input.GetMouseButtonDown(1) && focusedModel != null)
            {
                focusedModel.ToggleIsolate();
            }
        }

        lastPosition = Input.mousePosition;
    }
```

> [!IMPORTANT]
> Se executar este código, vai notar que nada acontece. Isto porque mudar a transformação de um objeto não sincroniza automaticamente a mudança de estado para o servidor, por razões de desempenho. Em vez disso, ou tem de empurrar esta alteração de estado para o servidor manualmente, ou ativa o **SyncEveryFrame** no componente *RemoteEntitySyncObject.*

Abra o script **RemoteModelEntity** e adicione esta linha:

```csharp
    public void OnEnable()
    {
        ...

        localSyncObject.SyncEveryFrame = true;
    }
```

Executando o código novamente, você deve ser capaz de clicar à esquerda em um objeto e arrastá-lo ao redor.

## <a name="add-a-cut-plane"></a>Adicione um plano de corte

A última característica que queremos experimentar neste tutorial, é usar [aviões cortados.](../../overview/features/cut-planes.md) Um plano cortado corta partes de objetos renderizados, de modo a que possa supor dentro deles.

Crie um novo GameObject na cena **CutPlane**. Crie um novo script e chame-o **de RemoteCutPlane**. Adicione o componente ao novo GameObject.

Abra o ficheiro script e substitua o seu conteúdo pelo seguinte código:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteCutPlane : MonoBehaviour
{
    private ARRCutPlaneComponent localCutPlaneComponent = null;
    private RemoteEntitySyncObject remoteEntitySync = null;

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            // can't do anything while we are not connected
            return;
        }

        if (localCutPlaneComponent == null)
        {
            localCutPlaneComponent = gameObject.CreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
        }

        if (remoteEntitySync == null)
        {
            remoteEntitySync = gameObject.GetComponent<RemoteEntitySyncObject>();
            remoteEntitySync.SyncEveryFrame = true;
        }

        localCutPlaneComponent.RemoteComponent.Normal = Axis.X;
        localCutPlaneComponent.RemoteComponent.FadeLength = 0.025f;
        localCutPlaneComponent.RemoteComponent.FadeColor = new Color4Ub(255, 128, 0, 255);
        localCutPlaneComponent.RemoteComponent.Enabled = true;
    }

    void OnDisable()
    {
        if (localCutPlaneComponent && localCutPlaneComponent.IsComponentValid)
        {
            localCutPlaneComponent.RemoteComponent.Enabled = false;
        }

        if (remoteEntitySync && remoteEntitySync.IsEntityValid)
        {
            remoteEntitySync.SyncEveryFrame = false;
        }
    }
}
```

Quando executar o seu código agora, deve ver como o modelo é aberto pelo avião. Pode selecionar o objeto *CutPlane* e movê-lo e rodá-lo na janela *cena.* Pode alternar o avião cortado de vez em quando, desativando o objeto de avião cortado.

## <a name="next-steps"></a>Passos seguintes

Agora conhece a funcionalidade mais importante para interagir com objetos remotos. No próximo tutorial, vamos dar uma olhada na personalização do visual de uma cena.

> [!div class="nextstepaction"]
> [Tutorial: Mudar o ambiente e os materiais](changing-environment-and-materials.md)
