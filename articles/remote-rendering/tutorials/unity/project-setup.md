---
title: Configurar um projeto do Unity do zero
description: Explica como configurar um projeto de Unidade em branco para a utilização com a Renderização Remota Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 33801316e4c0446865169560bb42f98052acba70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679598"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Tutorial: Criação de um projeto de unidade do zero

Neste tutorial, vai aprender:

> [!div class="checklist"]
>
> * Configurar um projeto de unidade de risco para ARR.
> * Criação e paragem de sessões de renderização.
> * Reutilizar as sessões existentes.
> * Ligação e desconexão das sessões.
> * Carregar modelos numa sessão de renderização.
> * Exibindo estatísticas de ligação.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial você precisa:

* Informação da sua conta (ID da conta, chave de conta, ID de subscrição). Se não tem uma conta, [crie uma conta.](../../how-tos/create-an-account.md)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unidade 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Instale estes módulos na Unidade:
    * **UWP** - Suporte universal para construir plataforma Windows
    * **IL2CPP** - Suporte para construção de janelas (IL2CPP)

> [!TIP]
> O [repositório](https://github.com/Azure/azure-remote-rendering) de amostras ARR contém projetos de unidade preparados para todos os tutoriais. Pode suster esses projetos como referência.

## <a name="create-a-new-unity-project"></a>Criar um novo projeto de Unidade

A partir do Centro de Unidade, crie um novo projeto.
Neste exemplo, assumimos que o projeto está a `RemoteRendering`ser criado numa pasta chamada .

![nova janela do projeto](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Configure o manifesto do projeto

Tem de modificar `Packages/manifest.json` o ficheiro que está localizado na pasta do projeto Unidade. Abra o ficheiro num editor de texto e apreenda as linhas listadas abaixo:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

O pacote de gasoduto de renderização Universal é opcional, mas recomendado por razões de desempenho.
Depois de modificar e guardar o manifesto, a Unidade irá automaticamente refrescar-se. Confirme que os pacotes foram carregados na janela do *Projeto:*

![confirmar importações de pacotes](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Certifique-se de que tem a versão mais recente do pacote

Os seguintes passos garantem que o seu projeto está a utilizar a versão mais recente do pacote de renderização remota.
1. Selecione o pacote na janela do ![Projeto e clique no ícone do pacote: Selecionando o ícone do pacote](media/package-icons.png)
1. No Inspetor, clique em "View ![in Package Manager": inspetor de pacotes](media/package-properties.png)
1. Na página do gestor do pacote para o pacote de renderização remota, veja se o botão de atualização está disponível. Se for, clicar ás irá atualizar o pacote ![para a versão mais recente disponível: O pacote ARR no gestor de pacotes](media/package-manager.png)
1. Por vezes, a atualização do pacote pode levar a erros na consola. Se isto ocorrer, tente fechar e reabrir o projeto.

## <a name="configure-the-camera"></a>Configure a câmara

Selecione o nó **da Câmara Principal.**

1. Redefinir a sua *Transformação:*

    ![redefinir a transformação da câmara](media/camera-reset-transform.png)

1. Definir **bandeiras claras** para a *cor sólida*

1. Definir **fundo** para *preto*

1. Coloque os **planos** de clipping para *perto = 0,3* e *Far = 20*. Isto significa que a renderização irá cortar a geometria que está mais perto de 30 cm ou mais distante do que 20 metros.

    ![Propriedades da câmara de unidade](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajuste as definições do projeto

1. Open *Edit > Project Settings...*
1. Na lista à esquerda selecione Qualidade.
1. Alterar o **Nível de Qualidade Padrão** para *Baixo*

    ![alterar as definições de qualidade do projeto](media/settings-quality.png)

1. Selecione **Gráficos** à esquerda.
1. Altere a definição de pipeline de **renderização scriptable** para *HybridRenderingPipeline*. Salte este passo se o gasoduto de renderização Universal não for utilizado.

    ![alterando as](media/settings-graphics-lwrp.png) definições gráficas do projeto Às vezes a UI não povoa a lista de tipos de pipeline disponíveis ![a partir das embalagens, caso em que o ativo *HybridRenderingPipeline* deve ser arrastado para o campo manualmente: alterar as definições gráficas do projeto](media/hybrid-rendering-pipeline.png)
1. Selecione **Jogador** à esquerda.
1. Selecione o separador de **definições** da Plataforma Universal windows
1. Alterar as **definições de XR** para suportar a realidade mista do Windows: ![definições do jogador](media/xr-player-settings.png)
1. Selecione as definições como na imagem acima:
    1. Ativar **a realidade virtual suportada**
    1. Definir **formato de profundidade** para profundidade de *16 bits*
    1. Ativar **a partilha do tampão** de profundidade
    1. Definir modo de **renderização estéreo** para *passe único atempouado*

1. Na mesma janela, acima das *Definições XR,* expandir **as Definições de Publicação**
1. Percorra até **Capacidades** e selecione:
    * **InternetClient**
    * **InternetClientServer**
    * **Perceção Espacial**
    * Opcional para desenvolvimento: **PrivateNetworkClientServer**

      Esta opção é necessária se quiser ligar o desordeiro remoto unidade ao seu dispositivo.

1. Em **Famílias de Dispositivos Suportados,** ative **Holographic** e **Desktop**

1. Se pretender utilizar o Kit de Ferramentas de Realidade Mista, consulte a [documentação MRTK,](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)para obter mais informações sobre as definições e capacidades recomendadas.

## <a name="validate-project-setup"></a>Validar a configuração do projeto

Execute os seguintes passos para validar que as definições do projeto estão corretas.

1. Escolha a entrada do ValidateProject a partir do menu RemoteRendering na barra de ferramentas do editor da Unidade.
1. Utilize a janela ValidateProject para verificar e corrigir as definições do projeto sempre que necessário.

    ![Validação do projeto editor de unidade](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Criar um script para inicializar a renderização remota de Azure

Crie um [novo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê-lhe o nome **RemoteRendering**. Abra o ficheiro script e substitua todo o seu conteúdo pelo código abaixo:

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

Este script inicializa a renderização remota azure, diga-lhe qual o objeto da câmara a utilizar para renderização e coloca um botão **Create Session** na porta de visão, quando o Modo *de Reprodução* estiver ativado.

> [!CAUTION]
> Modificar o script e guardá-lo enquanto o modo de jogo está ativo em Unidade pode resultar em congelamento de Unidade e você é forçado a desligá-lo através do gestor de tarefas. Por isso, pare sempre o modo de reprodução antes de editar o script *RemoteRendering.*

## <a name="test-azure-remote-rendering-session-creation"></a>Criação de sessão de renderização remota De teste Azure

Crie um novo GameObject na cena e adicione-lhe o componente *RemoteRendering.* Preencha o domínio de *conta*apropriado, id *de conta*e chave de *conta* para a sua conta de renderização remota:

![Propriedades dos componentes de renderização remota](media/remote-rendering-component.png)

Inicie a aplicação no editor **(prima Play** ou CTRL+P). Deve ver o botão **Create Session** aparecer no viewport. Clique nele para iniciar a sua primeira sessão de ARR:

![Criar uma primeira sessão](media/test-create.png)

Se isto falhar, certifique-se de que inseriu corretamente os dados da sua conta nas propriedades do componente RemoteRendering. Caso contrário, aparecerá uma mensagem na janela da consola mostrando o ID da sessão que lhe foi atribuído e afirmando que a sessão está atualmente no estado *de partida:*

![Saída de início da sessão](media/create-session-output.png)

Neste momento, o Azure está a fornecer um servidor para si e a iniciar uma máquina virtual de renderização remota. Isto normalmente **demora 3 a 5 minutos.** Quando o VM estiver pronto, `OnSessionStatusChanged` o nosso script de unidade é executado e irá imprimir o novo estado da sessão:

![Saída pronta da sessão](media/create-session-output-2.png)

É isso! Por enquanto, nada mais vai acontecer. Para evitar encargos, deve sempre parar as sessões quando já não são necessárias. Nesta amostra, pode fazê-lo clicando no botão **Stop Session** ou parando a simulação de Unidade. Devido à propriedade **Autosstop Session** no componente *ARRServiceUnity,* que está ligado por defeito, a sessão será interrompida automaticamente para si. Se tudo falhar, devido a falhas ou problemas de ligação, a sua sessão poderá decorrer durante o tempo que o seu *MaxLeaseTime* antes de ser desligado pelo servidor.

> [!NOTE]
> Parar uma sessão terá efeito imediato e não pode ser desfeito. Uma vez parado, tem que criar uma nova sessão, com a mesma despesa de arranque.

## <a name="reusing-sessions"></a>Reutilização de sessões

Criar uma nova sessão é, infelizmente, uma operação morosa. Por isso, deve-se tentar criar sessões raramente, e reutilizá-las sempre que possível.

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> Antes de executar este código, certifique-se de desativar a **opção Sessão de Paragem Automática** no componente RemoteRendering. Caso contrário, cada sessão que criar será automaticamente interrompida quando parar a simulação, e tentar reutilizá-la falhará.

Quando premir *Reprodução,* obtém agora três botões no viewport: **Create Session,** **Consulta Ative Sessions**, e Use A **Sessão Existente**. O primeiro botão cria sempre uma nova sessão. As segundas consultas de botão que existem sessões *ativas.* Se não especificou manualmente um ID da sessão para tentar utilizar, esta ação selecionará automaticamente esse ID da sessão para utilização futura. O terceiro botão tenta ligar-se a uma sessão existente. Ou um que especificou manualmente através da propriedade do componente *Session Id,* ou um encontrado pela *Query Ative Sessions*.

A função **AutoStartSessionAsync** é utilizada para simular as prensas de botões fora do editor.

> [!TIP]
> É possível abrir sessões que tenham sido interrompidas, expiradas ou em estado de erro. Embora já não possam ser usados para renderização, pode consultar os seus detalhes, uma vez que abriu uma sessão inativa. O código acima verifica o `ARRService_OnSessionStarted`estado de uma sessão, para parar automaticamente quando a sessão se tornar inutilizável.

Com esta funcionalidade, pode agora criar e reutilizar sessões, o que deverá melhorar significativamente o seu fluxo de trabalho de desenvolvimento.

Normalmente, a criação de uma sessão seria desencadeada fora da aplicação do cliente devido ao tempo necessário para girar o servidor.

## <a name="connect-to-an-active-session"></a>Ligue-se a uma sessão ativa

Até agora criámos ou abrimos sessões. O próximo passo é *ligar-se* a uma sessão. Uma vez ligado, o servidor de renderização produzirá imagens e enviará um fluxo de vídeo para a nossa aplicação.

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

Para testar esta funcionalidade:

1. Press **Play** in Unitity.
1. Abra uma sessão:
    1. Se já tiver uma sessão, prima **Consultas Ative Sessions** e, em seguida, use a **sessão existente**.
    1. Caso contrário, prima **Create Session**.
1. Prima **Ligar**.
1. Após alguns segundos, a saída da consola deve imprimir que está ligado.
1. Por enquanto, nada mais deve acontecer.
1. Prima **Desligue** ou pare o modo de reprodução da Unidade.

>[!NOTE]
> Vários utilizadores podem *abrir* uma sessão para consultar as suas informações, mas apenas um utilizador pode ser *ligado* a uma sessão de cada vez. Se outro utilizador já estiver ligado, a ligação falhará com um erro de **aperto**de mão .

## <a name="load-a-model"></a>Carregar um modelo

Insira o seguinte código no script *RemoteRendering* e remova as versões antigas das funções duplicadas:

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

Quando agora premir a reprodução, abra uma sessão e ligue-a, aparece o botão **Load Model.** Depois de clicar nele, a saída da consola mostrará o progresso de carregamento e quando chegar a 100% deve ver aparecer o modelo de um motor:

![Modelo carregado no editor](media/model-loaded-replace-me.png)

O [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) é um componente importante usado para a [estabilidade do holograma.](https://docs.microsoft.com/windows/mixed-reality/hologram-stability) No entanto, só terá efeito quando implantado num dispositivo de Realidade Mista.

> [!TIP]
> Se seguiu o [Quickstart: Converta um modelo para renderização,](../../quickstarts/convert-model.md)já sabe como converter os seus próprios modelos. Tudo o que precisa fazer agora para o renderizar, é colocar o URI num modelo convertido na propriedade *Nome Modelo.*

## <a name="display-frame-statistics"></a>Estatísticas de quadros de exibição

A Renderização Remota Azure rastreia várias informações sobre a qualidade da ligação. Para uma forma rápida de exibir esta informação, faça o seguinte:

Crie um [novo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê-lhe o nome **RemoteFrameStats**. Abra o ficheiro script e substitua todo o seu conteúdo pelo código abaixo:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

Crie um GameObject e nomeie-o *FrameStats*. Prenda-o como nó de criança ao objeto *da Câmara Principal* e coloque a sua posição em x = **0, y = 0, z = 0,325**. Adicione o componente **RemoteFrameStats** ao objeto.

Adicione um objeto de criança **de Tela de > UI** ao objeto *FrameStats* e detete as suas propriedades desta forma:

![propriedades de lona](media/framestats-canvas.png)

Adicione um objeto **de texto > UI** como uma criança da tela e coloque as suas propriedades assim:

![propriedades de texto](media/framestats-text.png)

Selecione o objeto *FrameStats* e povoe o **campo FrameStats** clicando no ícone do círculo e selecionando o objeto **de texto:**

![definição de propriedade de texto](media/framestats-set-text.png)

Agora, quando ligado à sessão remota, o texto deve mostrar as estatísticas de streaming:

![saída de estatísticas de quadros](media/framestats-output.png)

O código desativa a atualização estatística fora do editor como uma caixa de texto trancada na cabeça seria uma distração. Uma implementação mais sofisticada encontra-se no projeto [Quickstart.](../../quickstarts/render-model.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu todos os passos necessários para tomar um projeto de Unidade em branco e fazê-lo trabalhar com a Renderização Remota Azure. No próximo tutorial, vamos ver mais de perto como trabalhar com entidades remotas.

> [!div class="nextstepaction"]
> [Tutorial: Trabalhar com entidades remotas na Unidade](working-with-remote-entities.md)
