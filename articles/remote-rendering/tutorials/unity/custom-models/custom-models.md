---
title: Interfaces e modelos personalizados
description: Adicione controladores de vista e ingere modelos personalizados para serem renderizados pela Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591668"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Tutorial: Interfaces e modelos personalizados

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Adicione o Kit de Ferramentas de Realidade Mista ao projeto
> * Gerir o estado do modelo
> * Configure Azure Blob Storage para ingestão de modelos
> * Upload e modelos de processo para renderização

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se no [Tutorial: Visualizar um modelo renderizado remotamente.](../view-remote-models/view-remote-models.md)

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Começa com o Mixed Reality Toolkit (MRTK)

O Mixed Reality Toolkit (MRTK) é um kit de ferramentas de plataforma cruzada para a construção de experiências de realidade mista. Usaremos MRTK 2.5.1 para as suas funcionalidades de interação e visualização.

Para adicionar MRTK, siga as [etapas requeridas listadas](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) no [Guia de Instalação MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

Esses passos são:
 - [Receba os mais recentes pacotes mrtk unidade](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Mesmo que diga "mais recente", por favor use a versão 2.5.1 da página de lançamento mrtk.
     - Só usamos o pacote da *Fundação* neste tutorial. As *extensões,* *ferramentas* e *pacotes exemplos* não são necessárias.
 - [Importe pacotes MRTK no seu projeto de unidade](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Mude o seu projeto de Unidade para a plataforma-alvo](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Devia ter dado este passo já no primeiro capítulo, mas agora é uma boa altura para verificar duas vezes!
 - [Adicione MRTK a uma nova cena ou projeto novo](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Pode adicionar MRTK a uma nova cena e adicionar novamente o seu coordenador e objetos/scripts de modelo, ou pode adicionar MRTK à sua cena existente utilizando o comando de *ferramentas de realidade mista -> Adicionar à Cena e configurar* o comando do menu.

## <a name="import-assets-used-by-this-tutorial"></a>Importar ativos utilizados por este tutorial

A partir deste capítulo, implementaremos um simples [padrão de controlador de visão de modelo](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) para grande parte do material coberto. A parte *modelo* do padrão é o código específico de renderização remota Azure e a gestão do estado relacionada com a renderização remota Azure. A *vista* e as partes *controladoras* do padrão são implementadas usando ativos MRTK e alguns scripts personalizados. É possível utilizar o *modelo* neste tutorial sem que o *controlador de visão* seja implementado aqui. Esta separação permite-lhe integrar facilmente o código encontrado neste tutorial na sua própria aplicação, onde assumirá a parte do *controlador de visualização* do padrão de design.

Com a introdução do MRTK, há uma série de scripts, pré-fabricados e ativos que podem agora ser adicionados ao projeto para apoiar interações e feedback visual. Estes ativos, referidos como **Ativos Tutoriais,** são agregados num [Pacote de Ativos de Unidade,](https://docs.unity3d.com/Manual/AssetPackages.html)que está incluído no [GitHub de renderização remota Azure](https://github.com/Azure/azure-remote-rendering) em '\Unity\TutorialAssets\TutorialAssets.unitypackage'.

1. Clone ou descarregue o repositório [git Azure Remote Rendering,](https://github.com/Azure/azure-remote-rendering)se descarregar o fecho para um local conhecido.
1. No seu projeto Unidade, escolha *Pacote de Importação -> -> Pacote Personalizado.*
1. No explorador de ficheiros, navegue para o diretório onde clonou ou desapertou o repositório de renderização remota Azure, selecione a .unitypackage encontrada em **Unity -> TutorialAssets -> TutorialAssets.unitypackage**
1. Selecione o botão **Importar** para importar o conteúdo da embalagem para o seu projeto.
1. No Editor de Unidade, selecione *Mixed Reality Toolkit -> Utilities -> Atualizar o MRTK Standard Shader para* o Pipeline de renderização leve a partir da barra de menu superior e siga as instruções para atualizar o shader.

Assim que o MRTK e os Ativos Tutoriais estiverem incluídos no projeto, mudaremos o perfil MRTK para mais um adequado para o tutorial.

1. Selecione o **Jogo De Realidade MistaToolkit** na hierarquia da cena.
1. No Inspetor, sob o componente **MixedRealityToolkit,** altere o perfil de configuração para *ARRMixedRealityToolkitConfigurationProfile*.
1. Prima *Ctrl+S* para guardar as suas alterações.

Isto configurará o MRTK, principalmente, com os perfis HoloLens 2 padrão padrão. Os perfis fornecidos são pré-configurados das seguintes formas:
 - Desligue o perfil (Prima 9 para alternar/desligar ou diga "Mostrar/Ocultar" no dispositivo).
 - Desligue o cursor do olhar dos olhos.
 - Ativar cliques de rato de unidade, para que possa clicar em elementos de UI MRTK com o rato em vez da mão simulada.

## <a name="add-the-app-menu"></a>Adicione o Menu de Aplicações

A maioria dos controladores de visão neste tutorial operam contra classes base abstratas em vez de contra classes concretas. Este padrão proporciona mais flexibilidade e permite-nos fornecer os controladores de vista para si, ao mesmo tempo que o ajuda a aprender o código de renderização remota Azure. Para simplificar, a classe **RemoteRenderingCoordinator** não tem uma classe abstrata fornecida e o seu controlador de visão funciona diretamente contra a classe de betão.

Agora pode adicionar o **AppMenu** pré-fabricado à cena, para obter feedback visual do estado atual da sessão. Este controlador de visualização irá "desbloquear" mais controladores de visualização de menus sub à medida que implementamos e integramos mais funcionalidades de ARR na cena. Para já, o **AppMenu** terá uma indicação visual do estado ARR e apresentará o painel modal que o utilizador utiliza para autorizar a aplicação a ligar-se ao ARR.

1. Localizar o **prefácio appMenu** em *Ativos/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Arraste o **prefácio appMenu** para o local.
1. É provável que veja um diálogo para o **Importador de TMP,** já que é a primeira vez que estamos a incluir os ativos da *Text Mesh Pro* no local. Siga as instruções para **importar a Import TMP Essentials**. Em seguida, feche o diálogo do importador, os exemplos e extras não são necessários.
1. O **AppMenu** está configurado para ligar automaticamente e fornecer o modal para consentir a ligação a uma Sessão, para que possamos remover o bypass colocado mais cedo. No **RemoteRenderingCoordinator** GameObject, remova o bypass para autorização que implementamos anteriormente, premindo o botão '-' no evento **Autorização De Pedido.**
 ![Remover o bypass ](./media/remove-bypass-event.png) .
1. Teste o controlador de visualização pressionando **Play** no Editor de Unidade.
1. No Editor, agora que o MRTK está configurado, pode utilizar as teclas WASD para alterar a posição da sua visão e premir o botão do rato direito + mover o rato para alterar a direção de visualização. Tente "dirigir" ao redor do local um pouco para sentir os controlos.
1. No dispositivo, pode levantar a palma da mão para convocar o **AppMenu,** no Editor de Unidade, utilizar o 'M' da chave quente.
1. Se perdeu de vista o menu, prima a tecla 'M' para convocar o menu. O menu será colocado perto da câmara para facilitar a interação.
1. A autorização irá agora mostrar como um pedido à direita do **AppMenu**, a partir de agora, irá utilizar esta para autorizar a aplicação a gerir sessões de renderização remotas.
 ![UI autorizar](./media/authorize-request-ui.png)
1. Impedir a Unidade de jogar para continuar com o tutorial.

## <a name="manage-model-state"></a>Gerir o estado do modelo

Agora vamos implementar um novo script, **RemoteRenderedModel** que é para rastrear o estado, respondendo a eventos, eventos de disparo e configuração. Essencialmente, **o RemoteRenderedModel** armazena o caminho remoto para os dados do modelo em `modelPath` . Irá ouvir alterações de estado no **RemoteRenderingCoordinator** para ver se deve carregar ou descarregar automaticamente o modelo que define. O GameObject que tem o **RemoteRenderedModel** ligado ao mesmo será o pai local para o conteúdo remoto.

Note que o script **RemoteRenderedModel** implementa **o Modelo De BaseRemoteRendered,** incluído a partir de **Ativos Tutoriais**. Isto permitirá que o controlador de visualização do modelo remoto se ligue ao seu script.

1. Crie um novo script chamado **RemoteRenderedModel** na mesma pasta que o **RemoteRenderingCoordinator**. Substitua todo o conteúdo pelo seguinte código:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

Nos termos mais básicos, o **RemoteRenderedModel** detém os dados necessários para carregar um modelo (neste caso, o SAS ou *builtin://* URI) e rastreia o estado do modelo remoto. Quando é hora de carregar, o `LoadModel` método é chamado no **RemoteRenderingCoordinator** e a Entidade que contém o modelo é devolvida para referência e descarga.

## <a name="load-the-test-model"></a>Carregue o modelo de teste

Vamos testar o novo script carregando o modelo de teste novamente. Criaremos um Game Object para conter o script e ser um dos pais do modelo de teste.

1. Crie um novo Objeto de Jogo vazio na cena e nomeie-o **TestModel**.
1. Adicione o script *RemoteRenderedModel* ao **TestModel**.
![Adicionar componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Preencha `Model Display Name` o e o com " `Model Path` *TestModel*" e "*builtin://Engine*" respectivamente.
![Especifique detalhes do modelo](./media/add-model-script.png)
1. Posicione o objeto **TestModel** na frente da câmara, na **posição x = 0, y = 0, z = 3**.
![Objeto de posição](./media/test-model-position.png)
1. Certifique-se de que **o Carregamento Automaticamente** está ligado.
1. Pressione **play** no Editor de Unidade para testar a aplicação.
1. Conceder autorização clicando no botão *Ligar* para permitir que a aplicação crie uma sessão e irá ligar-se a uma Sessão e carregar automaticamente o modelo.

Observe a Consola à medida que a aplicação progride através dos seus estados. Lembre-se, alguns estados podem demorar algum tempo a completar, e não mostrarão progresso. Eventualmente, verá os registos do carregamento do modelo e, em seguida, o modelo de teste será renderizado na cena.

Tente mover e rodar o **TestModel** GameObject através do Transform in the Inspetor, ou na vista cena. Verá o modelo mover-se e rodá-lo na vista do Jogo.

![Diário de Unidade](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Provisão Blob Storage em Azure e ingestão de modelos personalizados

Agora podemos tentar carregar o seu próprio modelo. Para isso, terás de configurar o Blob Storage e no Azure, carregar e converter um modelo, e depois carregaremos o modelo utilizando o script **RemoteRenderedModel.** Os passos de carregamento personalizados do modelo podem ser ignorados com segurança se não tiver o seu próprio modelo para carregar neste momento.

Siga os passos especificados no [Quickstart: Converta um modelo para renderização](../../../quickstarts/convert-model.md). Ignore o **novo modelo de Inserção na secção Quickstart Sample App** para efeitos deste tutorial. Assim que tiver o URI de *Assinatura de Acesso Partilhado (SAS)* do seu modelo, continue até ao passo seguinte abaixo.

## <a name="load-and-rendering-a-custom-model"></a>Carregar e renderizar um modelo personalizado

1. Crie um novo GameObject vazio na cena e dê-lhe um nome semelhante ao seu modelo personalizado.
1. Adicione o script *RemoteRenderedModel* ao recém-criado GameObject.
 ![Adicionar componente RemoteRenderedModel](./media/add-remote-rendered-model-script.png)
1. Preencha o `Model Display Name` nome adequado para o seu modelo.
1. Preencha o `Model Path` com o URI de Assinatura de *Acesso Partilhado (SAS)* do modelo que criou nos degraus de ingestão acima.
1. Posicione o GameObject na frente da câmara, na **posição x = 0, y = 0, z = 3.**
1. Certifique-se de que **o Carregamento Automaticamente** está ligado.
1. Pressione **play** no Editor de Unidade para testar a aplicação.

    Verá que a Consola começa a povoar-se com o estado atual e, eventualmente, modelar mensagens de progresso. O seu modelo personalizado irá então carregar para a cena.

1. Remova o seu objeto modelo personalizado da cena do crime. A melhor experiência para este tutorial será usar o modelo de teste. Apesar de vários modelos serem certamente suportados em ARR, este tutorial foi escrito para melhor suportar um único modelo remoto de cada vez.

## <a name="next-steps"></a>Passos seguintes

Agora pode carregar os seus próprios modelos na Renderização Remota Azure e vê-los na sua aplicação! Em seguida, vamos guiá-lo através da manipulação dos seus modelos.

> [!div class="nextstepaction"]
> [Seguinte: Manipular modelos](../manipulate-models/manipulate-models.md)
