---
title: Ver um modelo composto remotamente
description: O "Hello World" da Renderização Remota Azure, tutorial mostra como ver um Modelo prestado remotamente por Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b98f5253cc60edc8949c06a645d81849dff2de7c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543691"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Tutorial: Visualização de um modelo renderizado remotamente

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Disposição de uma instância de renderização remota Azure (ARR)
> * Criar e parar uma sessão de renderização
> * Reutilizar uma sessão de renderização existente
> * Conecte-se e desconecte-se das sessões
> * Carregue os modelos numa sessão de renderização

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial você precisa:

* Uma subscrição ativa pay-as-you-go Azure [Criar uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A mais recente versão do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unidade (ver [requisitos do sistema](../../../overview/system-requirements.md#unity) para versões suportadas)
* Conhecimento intermédio da Unidade e da língua C# (por exemplo: criação de scripts e objetos, usando pré-fabricados, configurando eventos de unidade, etc.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Disposição de uma instância de renderização remota Azure (ARR)

Para ter acesso ao serviço de renderização remota Azure, primeiro precisa [de criar uma conta.](../../../how-tos/create-an-account.md#create-an-account)

## <a name="create-a-new-unity-project"></a>Criar um novo projeto do Unity

> [!TIP]
> O [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering) contém um projeto com todos os tutoriais concluídos, pode ser usado como referência. Veja em *Unidade\Tutorial-Completo* para o projeto completo de Unidade.

A partir do Centro de Unidade, crie um novo projeto.
Neste exemplo, assumimos que o projeto está a ser criado numa pasta chamada **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Novo Projeto de Unidade":::

## <a name="include-the-azure-remote-rendering-package"></a>Incluir o pacote de renderização remota Azure

[Siga as instruções](../../../how-tos/unity/install-remote-rendering-unity-package.md) sobre como adicionar o pacote de renderização remota Azure a um Projeto de Unidade.


## <a name="configure-the-camera"></a>Configure a câmara

1. Selecione o nó **da câmara principal.**

1. Abra o menu de contexto clicando no componente *Transform* e selecione a opção **Reset:**

    ![transformação de câmara de reset](./media/camera-reset-transform.png)

1. Definir **bandeiras claras** para a *cor sólida*

1. **set Background** to *Black* (#000000), com alfa totalmente transparente (0) alfa (A)

    ![Roda de cor](./media/color-wheel-black.png)

1. Definir **planos de clipping** para *perto = 0,3* e longe = *20*. Isto significa que a reprodução irá cortar a geometria que está mais perto de 30 cm ou mais longe que 20 metros.

    ![Propriedades de câmara de unidade](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajuste as definições do projeto

1. Abra *as definições > projeto de edição de edição...*
1. Selecione **Qualidade** do menu da lista esquerda
1. Alterar o Nível de **Qualidade Padrão** de todas as plataformas para *Baixo*. Esta definição permitirá uma renderização mais eficiente dos conteúdos locais e não afeta a qualidade dos conteúdos prestados remotamente.

    ![alterar definições de qualidade do projeto](./media/settings-quality.png)

1. Selecione **Gráficos** do menu da lista esquerda
1. Altere a definição **de Pipeline de Renderização Scriptable** para *HybridRenderingPipeline*.\
    ![Screenshot que aponta onde você muda a definição de Pipeline de renderização Scriptable para HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Por vezes, a UI não preenche a lista dos tipos de gasodutos disponíveis das embalagens. Se isto ocorrer, o ativo *HybridRenderingPipeline* deve ser arrastado para o campo manualmente:\
    ![alterando as definições de gráficos do projeto](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Se não conseguir arrastar e largar o ativo *HybridRenderingPipeline* no campo Render Pipeline Asset (possivelmente porque o campo não existe!), certifique-se de que a sua configuração do pacote contém o `com.unity.render-pipelines.universal` pacote.

1. Selecione **Player** do menu da lista esquerda
1. Selecione o separador **de definições da Plataforma Universal do Windows,** representado como um ícone do Windows.
1. Altere as **definições de XR** para suportar a realidade mista do Windows, como mostrado abaixo:
    1. Ativar **a realidade virtual suportada**
    1. Prima o botão '+' e adicione **realidade mista do Windows**
    1. Definir **formato de profundidade** para profundidade de *16 bits*
    1. Certifique-se de que **a partilha do tampão de profundidade** está ativada
    1. Definir **modo de renderização estéreo** para *single pass instanced*

    ![definições de jogador](./media/xr-player-settings.png)

1. Na mesma janela, acima de **Definições XR,** expandir **Definições de Publicação**
1. Desloque-se para baixo para **as capacidades** e selecione:
    * **InternetClient**
    * **InternetClientServer**
    * **Perceção Espacial**
    * **PrivateNetworkClientServer** *(opcional).* Selecione esta opção se pretender ligar o depurar remoto Unidade ao seu dispositivo.

1. Sob **as famílias de dispositivos suportados,** ativar o **Holográfico** e **o Desktop**
1. Feche ou doca o painel **de Definições** do Projeto
1. Abrir *definições de construção de >de ficheiros*
1. Selecione **plataforma universal do Windows**
1. Configure as suas definições para corresponder às encontradas abaixo
1. Prima o botão **'Plataforma switch'.**
![configurações de construção](./media/build-settings.png)
1. Depois de a Unidade mudar de plataforma, feche o painel de construção.

## <a name="validate-project-setup"></a>Validar a configuração do projeto

Execute os seguintes passos para validar que as definições do projeto estão corretas.

1. Escolha a entrada **ValidateProject** a partir do menu **RemoteRendering** na barra de ferramentas do editor Unidade.
1. Reveja a janela **ValidateProject** para obter erros e corrija as definições do projeto sempre que necessário.

    ![Validação do projeto do editor de unidade](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Criar um script para coordenar a ligação e estado de renderização remota do Azure

Existem quatro estágios básicos para mostrar modelos remotamente renderizados, delineados no fluxograma abaixo. Cada etapa deve ser executada por ordem. O próximo passo é criar um script que gere o estado de aplicação e prossiga através de cada fase necessária.

![Pilha de ARR 0](./media/remote-render-stack-0.png)

1. No *painel de projetos,* em **Ativos,** crie uma nova pasta chamada *RemoteRenderingCore*. Em seguida, dentro *do RemoteRenderingCore,* crie outra pasta chamada *Scripts*.

1. Crie um [novo script C#](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) chamado **RemoteRenderingCoordinator**.
O seu projeto deve ser assim:

    ![Hierarquia do projeto](./media/project-structure.png)

    Este script coordenador irá rastrear e gerir o estado de renderização remota. De notar que alguns destes códigos são utilizados para manter o estado, expondo a funcionalidade a outros componentes, desencadeando eventos e armazenando dados específicos da aplicação que não estão *diretamente* relacionados com a Renderização Remota Azure. Use o código abaixo como ponto de partida, e iremos endereçar e implementar o código de renderização remota Azure mais tarde no tutorial.

1. Abra **o RemoteRenderingCoordinator** no seu editor de código e substitua todo o seu conteúdo pelo código abaixo:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Criar o Azure Remote Rendering GameObject

O coordenador de renderização remota e o seu script requerido *(ARRServiceUnity)* são ambos monoBehaviours que devem ser ligados a um GameObject na cena. O script *ARRServiceUnity* é fornecido pela ARR para expor grande parte da funcionalidade do ARR para ligar e gerir sessões remotas.

1. Crie um novo GameObject na cena (Ctrl+Shift+N ou *GameObject->Create Empty*) e nomeie-o **RemoteRenderingCoordinator**.
1. Adicione o script *RemoteRenderingCoordinator* ao **RemoteRenderingCoordinator** GameObject.\
![Adicionar componente RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Confirme que o script *ARRServiceUnity,* que aparece como *Serviço* no inspetor, é automaticamente adicionado ao GameObject. Caso esteja a pensar, este é um resultado que está `[RequireComponent(typeof(ARRServiceUnity))]` no topo do script **RemoteRenderingCoordinator.**
1. Adicione as suas credenciais de renderização remota Azure, o seu Domínio de Autenticação de Conta e o Domínio da Conta no script do coordenador:\
![Adicione as suas credenciais](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicialize renderização remota de Azure

Agora que temos o quadro para o nosso coordenador, implementaremos cada uma das quatro etapas a começar pela **Renderização Remota Inicializada.**

![Pilha de ARR 1](./media/remote-render-stack-1.png)

**Inicialize** diz à Azure Remote Rendering qual a câmara que se opõe a utilizar para renderização e progride a máquina estatal para **não ser apreendida**. Isto significa que é inicializado, mas ainda não autorizado a ligar-se a uma sessão. Uma vez que iniciar uma sessão de ARR incorre num custo, precisamos confirmar que o utilizador quer prosseguir.

Ao entrar no estado **não australiano,** o **CheckAuthorization** é chamado, que invoca o evento **de Autorização de Pedidos** e determina quais as credenciais de conta a utilizar **(A AccountInfo** é definida perto do topo da classe e utiliza as credenciais que definiu através do Inspetor de Unidade no degrau acima).

   > [!NOTE]
   > A recompilação do tempo de funcionaamento não é suportada pela ARR. Modificar o script e guardá-lo enquanto o modo de reprodução está ativo pode resultar no congelamento da Unidade e na necessidade de forçar o encerramento através do gestor de tarefas. Certifique-se sempre de que parou o modo de reprodução antes de editar os seus scripts.

1. Substitua o conteúdo do **InitializeARR** e **do Serviço de Sesessão Inicialize** pelo código preenchido abaixo:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Para progredir de **Não-Eutanásia** a **NoSession,** normalmente apresentamos um diálogo modal ao utilizador para que eles possam escolher (e faremos isso mesmo noutro capítulo). Por enquanto, iremos contornar automaticamente a verificação de autorização ligando para **a ByPassAuthentication** assim que o evento **de Autorização de Pedidos** for desencadeado.

1. Selecione o **RemoteRenderingCoordinator** GameObject e encontre o Evento de Unidade de **Outoria OnRequesting** exposto no Inspetor do Componente **RemoteRenderingCoordinator.**

1. Adicione um novo evento premindo o '+' no direito inferior.
1. Arraste o componente para o seu próprio evento, para se referir a si próprio.
![Autenticação do bypass](./media/bypass-authorization-add-event.png)\
1. Na queda para baixo **selecione RemoteRenderingCoordinator -> BypassAuthorization**.\
![Screenshot que mostra a opção remoteRenderingCoordinator.BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Criar ou juntar-se a uma sessão remota

A segunda fase é criar ou juntar uma sessão de renderização remota (ver [Sessões de Renderização Remota](../../../concepts/sessions.md) para obter mais informações).

![Pilha de ARR 2](./media/remote-render-stack-2.png)

A sessão remota é onde os modelos serão renderizados. O método **JoinRemoteSession** tentará aderir a uma sessão existente, acompanhada com a propriedade **LastUsedSessionID** ou se houver um ID de sessão ativa atribuído no **SessionIDOverride**. **SessionIDOverride** destina-se apenas aos seus fins de depuragem, só deve ser utilizado quando souber que a sessão existe e gostaria de se ligar explicitamente a ela.

Se não houver sessões disponíveis, será criada uma nova sessão. A criação de uma nova sessão é, no entanto, uma operação morosa. Por isso, deverá tentar criar sessões apenas quando necessário e reutilizá-las sempre que possível (ver [Conjunto Comercial: Reunião de Sessão, agendamento e melhores práticas](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) para obter mais informações sobre sessões de gestão).

> [!TIP]
> **StopRemoteSsion()** terminará a sessão ativa. Para evitar cargas desnecessárias, deve sempre parar as sessões quando já não são necessárias.

A máquina estatal irá agora progredir para **connectingToNewRemoteSession** ou **ConnectingToExistingRemoteSsion,** dependendo das sessões disponíveis. Tanto a abertura de uma sessão existente como a criação de uma nova sessão irá desencadear o evento **ARRSessionService.OnSessionStatusChanged,** executando o nosso método **OnRemoteSessionStatusChanged.** Idealmente, isto resultará no avanço da máquina estatal para **RemoteSessionReady**.

1. Para aderir a uma nova sessão, modifique o código para substituir os **métodos Desessessão de Medaremotession** e **StopRemoteSssion** pelos exemplos preenchidos abaixo:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Se pretender economizar tempo reutilizando sessões, certifique-se de desativar a **sessão de paragem automática** da opção no componente *ARRServiceUnity.* Tenha em mente que isto deixará as sessões em funcionamento, mesmo quando ninguém está ligado a elas. A sessão pode decorrer enquanto o seu *MaxLeaseTime* for desligado pelo servidor (o valor para *MaxLeaseTime* pode ser modificado no Coordenador de Renderização Remota, em *Padrão de Nova Sessão).* Por outro lado, se desligar automaticamente todas as sessões ao desligar-se, terá de esperar sempre que uma nova sessão seja iniciada, o que pode ser um processo um pouco moroso.

> [!NOTE]
> Parar uma sessão terá efeito imediato e não pode ser desfeito. Uma vez parado, tem que criar uma nova sessão, com a mesma sobrecarga de arranque.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Ligue o tempo de execução local à sessão remota

Em seguida, a aplicação precisa de ligar o seu tempo de funcionamento local à sessão remota.

![Pilha de ARR 3](./media/remote-render-stack-3.png)

A aplicação também precisa de ouvir os acontecimentos sobre a ligação entre o tempo de funcionamento e a sessão atual; essas alterações de estado são tratadas no **OnLocalRuntimeStatusChanged**. Este código irá avançar o nosso estado para **ConnectingToRuntime**. Uma vez ligado no **OnLocalRuntimeStatusChanged,** o estado avançará para **RuntimeConnected**. A ligação ao tempo de funcionamento é o último estado com o qual o coordenador se preocupa, o que significa que a aplicação é feita com toda a configuração comum e está pronta para iniciar o trabalho específico de sessão de modelos de carregamento e renderização.

 1. Substitua os **métodos ConnectRuntimeToRemoteSssion** e **DisconnectRuntimeFromRemoteSssion** com as versões completas abaixo.
 1. É importante tomar nota do método de unidade **LateUpdate** e que está a atualizar a sessão ativa atual. Isto permite que a sessão atual envie/receba mensagens e atualize o tampão de quadro com os quadros recebidos da sessão remota. É fundamental para o funcionamento do ARR corretamente.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Ligar o tempo de funcionamento local a uma sessão remota depende da **chamada de Atualização** para a sessão atualmente ativa. Se descobrir que a sua aplicação nunca está a progredir para além do estado **de ConnectingToRuntime,** certifique-se de que está a ligar regularmente para a **Atualização** na sessão ativa.

## <a name="load-a-model"></a>Carregue um modelo

Com a base necessária, está pronto para carregar um modelo na sessão remota e começar a receber quadros.

![Diagrama que mostra o fluxo de processo para preparar para carregar e ver um modelo.](./media/remote-render-stack-4.png)

O método **LoadModel** foi concebido para aceitar um caminho modelo, manipulador de progresso e transformação dos pais. Estes argumentos serão utilizados para carregar um modelo na sessão remota, atualizar o utilizador sobre o progresso do carregamento e orientar o modelo renderizado remotamente com base na transformação dos pais.

1. Substitua o método **LoadModel** inteiramente pelo código abaixo:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

O código acima está a executar os seguintes passos:

1. Criar uma [Entidade Remota.](../../../concepts/entities.md)
1. Crie um GameObject local para representar a entidade remota.
1. Configure o GameObject local para sincronizar o seu estado (isto é, transformar) na entidade remota a cada frame.
1. Desacorda um nome e adicione um [**WorldAnchor**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) para ajudar na estabilização.
1. Carregue os dados do modelo do Blob Storage na entidade remota.
1. Devolva a Entidade-Mãe, para posterior referência.

## <a name="view-the-test-model"></a>Ver o modelo de teste

Dispomos agora de todo o código necessário para visualizar um modelo renderizado remotamente, todas as quatro fases necessárias para a renderização remota estão completas. Agora precisamos adicionar um pequeno código para iniciar o processo de carga do modelo.

![Pilha de ARR 4](./media/remote-render-stack-5.png)

1. Adicione o seguinte código à classe **RemoteRenderingCoordinator,** logo abaixo do método **LoadModel:**

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Este código cria um GameObject para agir como o progenitor do modelo de teste. Em seguida, chama o método **LoadModel** para carregar o modelo "builtin://Engine", que é um ativo incorporado na renderização remota Azure com o propósito de testar a renderização.

1. Guarde o seu código.
1. Prima o botão Reproduzir no Editor de Unidade para iniciar o processo de ligação à Renderização Remota Azure e criar uma nova sessão.
1. No entanto, não verás muito na vista do Jogo, no entanto, a Consola mostrará o estado da mudança da aplicação. Provavelmente progredirá para `ConnectingToNewRemoteSession` , e ficar lá, possivelmente por até cinco minutos.
1. Selecione o **RemoteRenderingCoordinator** GameObject para ver os scripts anexados no inspetor. Observe a atualização da componente **de Serviço** à medida que progride através dos seus passos de inicialização e de ligação.
1. Monitorize a saída da Consola - à espera que o estado mude para **RuntimeConnected**.
1. Uma vez ligado o tempo de funcionamento, clique com o botão direito no **RemoteRenderingCoordinator** no inspetor para expor o menu de contexto. Em seguida, clique na opção **Modelo de Teste de Carga** no menu de contexto, adicionada pela parte do nosso código `[ContextMenu("Load Test Model")]` acima.

    ![Carga do menu de contexto](./media/load-test-model.png)

1. Observe a Consola para a saída do **ProgressHandler** que passamos para o método **LoadModel.**
1. Veja o modelo prestado remotamente!

> [!NOTE]
> O modelo remoto nunca será visível na vista Cena, apenas na vista do jogo. Isto porque o ARR está a render os quadros remotamente especificamente para a perspetiva da câmara de visualização do Jogo e não está ciente da câmara do Editor (usada para tornar a vista de Cena).

## <a name="next-steps"></a>Passos seguintes

![Modelo carregado](./media/test-model-rendered.png)

Parabéns! Criou uma aplicação básica capaz de visualizar modelos remotamente renderizados utilizando a renderização remota do Azure. No próximo tutorial, vamos integrar o MRTK e importar os nossos próprios modelos.

> [!div class="nextstepaction"]
> [Seguinte: Interfaces e modelos personalizados](../custom-models/custom-models.md)
