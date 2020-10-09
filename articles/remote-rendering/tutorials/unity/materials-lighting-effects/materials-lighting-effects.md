---
title: Refinar materiais, iluminação e efeitos
description: Modificar os materiais do modelo e a iluminação. Adicione efeitos adicionais como delinear e cortar aviões.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 0580614468d4003b3640fd4df08ff02f3a1c8476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021073"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Tutorial: Materiais de refinação, iluminação e efeitos

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Modelos de destaque e contorno e componentes de modelos
> * Aplicar diferentes materiais em modelos
> * Corte através de modelos com planos cortados
> * Adicione animações simples para objetos renderizados remotamente

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se em [Tutorial: Manipular modelos.](..\manipulate-models\manipulate-models.md)

## <a name="highlighting-and-outlining"></a>Realçar e delinear

Fornecer feedback visual ao utilizador é uma parte importante da experiência do utilizador em qualquer aplicação. A renderização remota Azure fornece mecanismos de feedback visual através de [sobreposições de estado hierárquicos](../../../overview/features/override-hierarchical-state.md). As sobreposições hierárquicas do estado são implementadas com componentes ligados a instâncias locais de modelos. Aprendemos a criar estes casos locais em [Sincronizar o gráfico de objetos remotos na hierarquia da Unidade.](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy)

Primeiro, vamos criar um invólucro em torno do componente [**HierarchicalStateOverrideComponent.**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) O **HierarchicalStateOverrideComponent** é o script local que controla as sobreposições na entidade remota. Os [**Ativos Tutoriais**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) incluem uma classe base abstrata chamada **BaseEntityOverrideController,** que vamos estender para criar o invólucro.

1. Crie um novo script chamado **EntityOverrideController** e substitua o seu conteúdo pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

O principal trabalho da **LocalOverride**é criar uma ligação entre si e a sua `RemoteComponent` . O **LocalOverride** permite-nos então colocar bandeiras do estado na componente local, que estão ligadas à entidade remota. As sobreposições e os seus estados são descritos na página [de sobreposições do estado hierárquico.](../../../overview/features/override-hierarchical-state.md) 

Esta implementação apenas alterna um estado de cada vez. No entanto, é inteiramente possível combinar múltiplas sobreposições em entidades individuais e criar combinações a diferentes níveis da hierarquia. Por exemplo, combinar `Selected` e num único componente `SeeThrough` dar-lhe-ia um esboço, tornando-o também transparente. Ou, definir a entidade raiz `Hidden` `ForceOn` sobrepondo-se ao fazer a substituição de uma entidade infantil para esconder `Hidden` `ForceOff` tudo, exceto para a criança com o sobreposição.

Para aplicar estados a entidades, podemos modificar a **RemoteEntityHelper** criada anteriormente.

1. Modifique a classe **RemoteEntityHelper** para implementar a classe abstrata **BaseRemoteEntityHelper.** Esta modificação permitirá a utilização de um controlador de vista fornecido nos **Ativos Tutoriais**. Deve ser assim quando modificado:

    ```csharp
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Sobrepor os métodos abstratos utilizando o seguinte código:

    ```csharp
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Este código garante que um componente **EntityOverrideController** é adicionado à Entidade-alvo, e depois chama um dos métodos de toggle. Se desejar, no **TestModel** GameObject, chamar estes métodos de ajuda pode ser feito adicionando o **RemoteEntityHelper** como uma chamada de volta ao `OnRemoteEntityClicked` evento no componente **RemoteRayCastPointerHandler.**

![Chamadas de ponteiro](./media/pointer-event-callbacks.png)

Agora que estes scripts foram adicionados ao modelo, uma vez ligados ao tempo de execução, o controlador de visualização **AppMenu** deverá ter interfaces adicionais habilitadas a interagir com o script **EntityOverrideController.** Consulte o menu **Model Tools** para ver os controladores de vista desbloqueados.

Neste ponto, os componentes do **TestModel** GameObject devem ser mais ou menos assim:

![Modelo de teste com scripts adicionais](./media/test-model-updated.png)

Aqui está um exemplo de empilhamento sobrepõe-se a uma única entidade. Usamos `Select` e `Tint` fornecemos um esboço e coloração:

![Seleção de tonalidade de modelo de teste](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Cortar planos

[Os aviões de](../../../overview/features/cut-planes.md) corte são uma característica que pode ser adicionada a qualquer entidade remota. Mais comummente, cria-se uma nova entidade remota que não está associada a quaisquer dados de malha para segurar o componente do plano de corte. A posição e orientação do plano de corte são determinadas pela posição e orientação da entidade remota a que está ligado.

Criaremos um script que cria automaticamente uma entidade remota, adiciona um componente de plano de corte e sincroniza a transformação de um objeto local com a entidade do plano de corte. Em seguida, podemos usar o **CutPlaneViewController** para embrulhar o plano de corte numa interface que nos permitirá manipulá-lo.

1. Crie um novo script chamado **RemoteCutPlane** e substitua o seu código pelo código abaixo:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Este código alarga a classe **BaseRemoteCutPlane** incluída nos **Ativos Tutoriais**. Da mesma forma que o modelo renderizado remotamente, este script anexa e ouve `RemoteRenderingState` alterações a partir do coordenador remoto. Quando o coordenador chegar ao `RuntimeConnected` estado, tentará ligar-se automaticamente, se for suposto. Há também uma `CutPlaneComponent` variável que vamos seguir. Este é o componente de renderização remota Azure que sincroniza com o plano de corte na sessão remota. Vamos ver o que temos de fazer para criar o avião cortado.

2. Substitua o `CreateCutPlane()` método pela versão completa abaixo:

    ```csharp
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Aqui estamos a criar uma entidade remota e a encaderna-la a um GameObject local. Garantimos que a entidade remota terá a sua transformação sincronizada com a transformação local, definindo `SyncEveryFrame` para `true` . Em seguida, usamos a `CreateComponent` chamada para adicionar um ao objeto `CutPlaneComponent` remoto. Finalmente, configuramos o plano de corte com as definições definidas no topo do MonoBehaviour. Vamos ver o que é preciso para limpar um plano cortado implementando o `DestroyCutPlane()` método.

3. Substitua o `DestroyCutPlane()` método pela versão completa abaixo:

    ```csharp
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Uma vez que o objeto remoto é bastante simples e estamos apenas a limpar a extremidade remota (e a manter o nosso objeto local), é simples ligar para `Destroy` o objeto remoto e limpar a nossa referência a ele.

O **AppMenu** inclui um controlador de visualização que se liga automaticamente ao seu plano de corte e permite interagir com ele. Não é necessário que use **o AppMenu** ou qualquer um dos controladores de visualização, mas eles fazem uma melhor experiência. Agora teste o plano de corte e o seu controlador de visão.

1. Crie um novo GameObject vazio na cena e nomeie-o **CutPlane**.
1. Adicione o componente **RemoteCutPlane** ao **CutPlane** GameObject.

   ![Configuração do componente do plano de corte](./media/cut-plane-config.png)

1. Prima Play no Editor de Unidade para carregar e ligar a uma sessão remota.
1. Utilizando a simulação manual do MRTK, agarre e rode (mantenha a Ctrl para rodar) o CutPlane para movê-lo em torno da cena do crime. Observe-o a cortar no **TestModel** para revelar componentes internos.

![Corte exemplo do avião](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Configurar a iluminação remota

A sessão de renderização remota suporta todo o espectro de opções de [iluminação](../../../overview/features/lights.md). Criaremos scripts para a Textura do [Céu](../../../overview/features/sky.md) e um mapa simples para dois tipos de luz de Unidade para usar com renderização remota.

### <a name="sky-texture"></a>Textura do céu

Há uma série de Cubemaps embutidos para escolher quando mudar a textura do céu. Estes são carregados na sessão e aplicados à textura do céu. Também é possível [carregar nas suas próprias texturas](../../../concepts/textures.md) para usar como uma luz do céu.

Vamos criar um script **RemoteSky** que tem uma lista dos Cubemaps disponíveis embutidos sob a forma de parâmetros de carga. Em seguida, permitiremos que o utilizador selecione e carregue uma das opções.

1. Crie um novo script chamado **RemoteSky** e substitua todo o seu conteúdo pelo código abaixo:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSASParams> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSASParams> builtInTextures = new Dictionary<string, LoadTextureFromSASParams>()
        {
            {"Autoshop",new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    A parte mais importante deste código são apenas algumas linhas:

    ```csharp
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Aqui, obtemos uma referência à textura a usar carregando-a na sessão a partir do armazenamento de bolhas incorporada. Então, só precisamos atribuir essa textura à sessão `SkyReflectionTexture` para aplicá-la.

1. Crie um GameObject vazio na sua cena e nomeie-o **SkyLight**.

1. Adicione o script **RemoteSky** ao seu **SkyLight** GameObject.

    A troca entre as luzes do céu pode ser feita ligando `SetSky` com uma das teclas de corda definidas em `AvailableCubemaps` . O controlador de visualização incorporado no  **AppMenu** cria automaticamente botões e liga os seus eventos para ligar `SetSky` com a respetiva chave.
1. Prima Play no Editor de Unidade e autorize uma ligação.
1. Depois de ligar o tempo de execução local a uma sessão remota, navegue **em AppMenu -> Session Tools -> Remote Sky** para explorar as diferentes opções do céu e ver como afetam o **TestModel**.

### <a name="scene-lights"></a>Luzes de cena

As luzes da cena remota incluem: ponto, ponto e direcional. Semelhantes ao Plano de Corte que criamos acima, estas luzes de cena são entidades remotas com componentes ligados a eles. Uma consideração importante ao acender a sua cena remota está a tentar combinar com a iluminação na cena local. Esta estratégia nem sempre é possível porque muitas aplicações de Unidade para os HoloLens 2 não usam renderização baseada fisicamente para objetos renderizados localmente. No entanto, a um certo nível, podemos simular a iluminação padrão mais simples da Unidade.

1. Crie um novo script chamado **RemoteLight** e substitua o seu código pelo código abaixo:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Este script cria diferentes tipos de luzes remotas dependendo do tipo de luz local de unidade a que o script está ligado. A luz remota duplicará a luz local na sua: posição, rotação, cor e intensidade. Sempre que possível, a luz remota também definirá uma configuração adicional. Isto não é um par perfeito, já que as luzes da unidade não são luzes PBR.

1. Encontre o **DirectionalLight** GameObject na sua cena. Se removeu o **DirectionalLight** padrão da sua cena: da barra de menu superior selecione *GameObject -> Light -> DirectionalLight* para criar uma nova luz na sua cena.

1. Selecione **o GameObject DirectionalLight** e utilize o botão **Adicionar Componente,** adicione o script **RemoteLight.**

1. Como este script implementa a classe `BaseRemoteLight` base, pode utilizar o controlador de visualização **appMenu** fornecido para interagir com a luz remota. Navegue para **AppMenu -> Ferramentas de Sessão - > Luz Direcional**.

    > [!NOTE]
    > A UI em **AppMenu** limitou-se a uma única luz direcional para a simplicidade. No entanto, ainda é possível e encorajado a adicionar luzes de ponto e a ligar-lhes o script **RemoteLight.** Essas luzes adicionais podem ser modificadas editando as propriedades da luz Unidade no editor. Necessitará de sincronizar manualmente as alterações locais à luz remota utilizando o menu de contexto **RemoteLight** no inspetor:
    >
    > ![Sincronização manual de luz remota](./media/sync-remote-light.png)

1. Prima Play no Editor de Unidade e autorize uma ligação.

1. Depois de ligar o tempo de funcionamento a uma sessão remota, posicione e aponte a sua câmara (use WASD e clique à direita + movimento do rato) para ter o controlador de vista de luz direcional à vista. 
1. Utilize o controlador de visualização de luz remota para modificar as propriedades da luz. Utilizando a simulação manual do MRTK, agarre e rode (mantenha a Ctrl para rodar) a luz direcional para ver o efeito na iluminação da cena.

    ![Luz direcional](./media/directional-light-test.png)

## <a name="editing-materials"></a>Materiais de edição

Os [materiais](../../../concepts/materials.md) renderizados remotamente podem ser modificados para fornecer efeitos visuais adicionais, afinar os visuais dos modelos renderizados ou fornecer feedback adicional aos utilizadores. Há muitas maneiras e muitas razões para modificar um material. Aqui, vamos mostrar-lhe como mudar a cor albedo de um material e alterar a rugosidade e metalidade de um material PBR.

> [!NOTE]
> Em muitos casos, se uma funcionalidade ou efeito pode ser implementado usando um **Computador HierárquicoStateOverrideComponent**, é ideal usá-lo em vez de modificar o material.

Criaremos um script que aceite uma Entidade-Alvo e configura alguns `OverrideMaterialProperty` objetos para alterar as propriedades do material da Entidade Alvo. Começamos por obter o [**MeshComponent**](../../../concepts/meshes.md#meshcomponent)da Entidade Alvo, que contém uma lista de materiais usados na malha. Para simplificar, usaremos o primeiro material encontrado. Esta estratégia ingénua pode falhar muito facilmente dependendo da forma como o conteúdo foi autoria, por isso provavelmente quereria ter uma abordagem mais complexa para selecionar o material apropriado.

A partir do material, podemos aceder a valores comuns como o albedo. Em primeiro lugar, os materiais precisam de ser fundidos no seu tipo `PbrMaterial` adequado, `ColorMaterial` ou, para recuperar os seus valores, como se vê no método **GetMaterialColor.** Assim que tivermos uma referência ao material desejado, basta definir os valores, e o ARR tratará da sincronização entre as propriedades do material local e o material remoto.

1. Crie um script chamado **EntityMaterialController** e substitua o seu conteúdo pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

O `OverrideMaterialProperty` tipo deve ser suficientemente flexível para permitir que alguns outros valores materiais sejam alterados, se desejar. O `OverrideMaterialProperty` tipo acompanha o estado de uma sobreposição, mantém o valor antigo e novo, e usa um delegado para definir a sobreposição. Como exemplo, `ColorOverride` veja-se:

```csharp
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Isto está a criar um novo `OverrideMaterialProperty` onde a sobreposição irá embrulhar o tipo `Color` . Fornecemos a cor atual ou original no momento em que a sobreposição é criada. Também lhe damos um material ARR para agir. Finalmente, é providenciado um delegado que aplicará a anulação. O delegado é um método que aceita um material ARR e o tipo que o sobrepõe envolve. Este método é a parte mais importante de entender como o ARR ajusta os valores materiais.

Utiliza `ColorOverride` o método para fazer o seu `ApplyMaterialColor` trabalho:

```csharp
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Este código aceita um material e uma cor. Verifica para ver que tipo de material é então faz um molde do material para aplicar a cor.

O `RoughnessOverride` e o trabalho da mesma forma - `MetalnessOverride` usando os e `ApplyRoughnessValue` `ApplyMetalnessValue` métodos para fazer o seu trabalho.

Em seguida, vamos testar o controlador de material.

1. Adicione o script **EntityMaterialController** ao seu **TestModel** GameObject.
1. Pressione Play in Unitity para iniciar a cena e ligar-se ao ARR.
1. Depois de ligar o seu tempo de funcionamento a uma sessão remota e carregar o modelo, navegue para **AppMenu -> Model Tools -> Editar Material**
1. Selecione uma Entidade do modelo utilizando as mãos simuladas para clicar no **TestModel**.
1. Confirme que o controlador de visualização de material **(AppMenu->Model Tools->Edit Material**) foi atualizado para a Entidade-Alvo.
1. Utilize o controlador de visualização de material para ajustar o material na Entidade-Alvo.

Como estamos apenas a modificar o primeiro material da malha, pode não ver o material a mudar. Use a sobreposição hierárquica **SeeThrough** para ver se o material que está a mudar está dentro da malha.

![Exemplo de edição de material](./media/material-edit-example.png)

## <a name="next-steps"></a>Passos seguintes

Parabéns! Implementou agora toda a funcionalidade principal da Renderização Remota Azure. No próximo capítulo, aprenderemos a assegurar o seu armazenamento Azure Remote Rendering e Blob. Estes serão os primeiros passos para lançar uma aplicação comercial que utiliza a Renderização Remota Azure.

> [!div class="nextstepaction"]
> [Seguinte: Assegurar a renderização remota do Azure e o armazenamento do modelo](../security/security.md)