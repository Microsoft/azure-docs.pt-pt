---
title: Configurar um projeto do Unity do zero
description: Explica como configurar um projeto de Unidade em branco para a utilização com renderização remota Azure.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: 7901f12763cb97fa76c0908e76755247ae934a20
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300594"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>Tutorial: Criação de um projeto de unidade do zero

Neste tutorial, vai aprender:

> [!div class="checklist"]
>
> * Configurar um projeto de unidade de risco para a ARR.
> * Criar e parar as sessões de renderização.
> * Reutilização das sessões existentes.
> * Ligação e desconexão das sessões.
> * Carregar modelos numa sessão de renderização.
> * Apresentando estatísticas de ligação.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial você precisa:

* Informação da sua conta (ID de conta, chave de conta, ID de subscrição). Se não tiver uma conta, [crie uma conta.](../../how-tos/create-an-account.md)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A versão mais recente do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/). 
* [Ferramentas de Estúdio Visual para Realidade Mista.](https://docs.microsoft.com/windows/mixed-reality/install-the-tools) Especificamente, as seguintes instalações *de carga de trabalho* são obrigatórias:
  * **Desenvolvimento de desktop com C++**
  * **Desenvolvimento da Plataforma Universal windows (UWP)**
* GIT [(download)](https://git-scm.com/downloads)
* Unidade 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Instale estes módulos na Unidade:
    * **UWP** - Suporte universal de construção de plataformas windows
    * **IL2CPP** - Suporte à Construção de Janelas (IL2CPP)

> [!TIP]
> O [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering) contém projetos de unidade preparados para todos os tutoriais. Podes usar esses projetos como referência.

## <a name="create-a-new-unity-project"></a>Criar um novo projeto de Unidade

A partir do Centro de Unidade, crie um novo projeto.
Neste exemplo, vamos assumir que o projeto está a ser criado numa pasta chamada `RemoteRendering` .

![nova janela do projeto](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>Configure o manifesto do projeto

Tem de modificar o ficheiro `Packages/manifest.json` que está localizado na pasta do projeto Unidade. Abra o ficheiro num editor de texto e apencha as linhas listadas abaixo:

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
Depois de modificar e guardar o manifesto, a Unidade irá automaticamente refrescar-se. Confirme que as embalagens foram carregadas na janela do *Projeto:*

![confirmar importações de pacotes](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>Certifique-se de que tem a versão mais recente do pacote

Os seguintes passos asseguram que o seu projeto está a utilizar a versão mais recente do pacote de renderização remota.
1. Selecione o pacote na janela do Projeto e clique no :::no-loc text="package"::: ícone: ![ Selecionar o ícone do pacote](media/package-icons.png)
1. No Inspetor, clique em "Ver em Gestor de Pacotes": ![ inspetor de pacotes](media/package-properties.png)
1. Na página do gestor de pacotes para o pacote de renderização remota, veja se o botão de atualização está disponível. Se for, em seguida, clicará nele irá atualizar o pacote para a versão mais recente disponível: ![ O pacote ARR no gestor de pacotes](media/package-manager.png)
1. Por vezes, a atualização do pacote pode levar a erros na consola. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="configure-the-camera"></a>Configure a câmara

Selecione o nó **da câmara principal.**

1. Repor a sua *transformação:*

    ![transformação de câmara de reset](media/camera-reset-transform.png)

1. Definir **:::no-loc text="Clear flags":::** para*:::no-loc text="Solid Color":::*

1. Definir **:::no-loc text="Background":::** para*:::no-loc text="Black":::*

1. Definir **:::no-loc text="Clipping Planes":::** o *de Perto = 0,3* e *Far = 20*. Isto significa que a reprodução irá cortar a geometria que está mais perto de 30 cm ou mais longe que 20 metros.

    ![Propriedades de câmara de unidade](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Ajuste as definições do projeto

1. Abra *as definições > projeto de edição de edição...*
1. Na lista à esquerda selecione Qualidade.
1. Alterar o **nível de qualidade predefinido** para *baixo*

    ![alterar definições de qualidade do projeto](media/settings-quality.png)

1. Selecione **Gráficos** à esquerda.
1. Altere a definição **de Pipeline de Renderização Scriptable** para *HybridRenderingPipeline*. Salte este passo se o gasoduto de renderização Universal não for utilizado.

    ![alteração das definições de gráficos de projeto ](media/settings-graphics-lwrp.png) Por vezes, a UI não preenche a lista de tipos de gasodutos disponíveis a partir dos pacotes, caso em que o ativo *HybridRenderingPipeline* deve ser arrastado para o campo manualmente: ![ alterando as definições de gráficos de projeto](media/hybrid-rendering-pipeline.png)
1. Selecione **Player** à esquerda.
1. Selecione o separador **de definições da Plataforma Universal windows**
1. Alterar as **definições de XR** para suportar realidade mista do Windows: ![ definições do jogador](media/xr-player-settings.png)
1. Selecione as definições como na imagem acima:
    1. Ativar **a realidade virtual suportada**
    1. Definir **formato de profundidade** para profundidade de *16 bits*
    1. Ativar a **partilha do tampão de profundidade**
    1. Definir **modo de renderização estéreo** para *single pass instanced*

1. Na mesma janela, acima de *Definições XR,* expandir **Definições de Publicação**
1. Desloque-se para baixo para **as capacidades** e selecione:
    * **InternetClient**
    * **InternetClientServer**
    * **Perceção Espacial**
    * Opcional para o desenvolvimento: **PrivateNetworkClientServer**

      Esta opção é necessária se pretender ligar o depurar remoto Unidade ao seu dispositivo.

1. Nas **Famílias de Dispositivos Suportados,** ativar **o Holográfico** e **o Desktop**

1. Se pretender utilizar o Conjunto de Ferramentas de Realidade Mista, consulte a [documentação mrtk,](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)para obter mais informações sobre as definições e capacidades recomendadas.

## <a name="validate-project-setup"></a>Validar a configuração do projeto

Execute os seguintes passos para validar que as definições do projeto estão corretas.

1. Escolha a entrada ValidateProject a partir do menu RemoteRendering na barra de ferramentas do editor Unidade.
1. Utilize a janela ValidateProject para verificar e corrigir as definições do projeto sempre que necessário.

    ![Validação do projeto do editor de unidade](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Criar um script para rubricar a renderização remota do Azure

Crie um [novo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê-lhe o nome **RemoteRendering**. Abra o ficheiro de script e substitua todo o seu conteúdo pelo código abaixo:

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

Este script inicializa a renderização remota do Azure, diz-lhe qual o objeto da câmara a utilizar para renderização e coloca um botão **De sessão de Criação** no viewport, quando o *Modo de Reprodução* é ativado.

> [!CAUTION]
> Modificar o script e guardá-lo enquanto o modo de reprodução estiver ativo na Unidade pode resultar no congelamento da Unidade e és forçado a desligá-lo através do gestor de tarefas. Portanto, pare sempre o modo de reprodução antes de editar o script *RemoteRendering.*

## <a name="test-azure-remote-rendering-session-creation"></a>Teste Azure Criação de sessão de renderização remota

Crie um novo GameObject na cena e adicione-lhe o componente *RemoteRendering.* Preencha o domínio de *conta*apropriado, *id de conta*e chave de *conta* para a sua conta de renderização remota:

![Propriedades de componentes de renderização remota](media/remote-rendering-component.png)

Inicie a aplicação no editor **(prima Play** ou CTRL+P). Deve ver o botão **'Criar Sessão'** aparecer no viewport. Clique nele para iniciar a sua primeira sessão de ARR:

![Criar uma primeira sessão](media/test-create.png)

Se isto falhar, certifique-se de que introduziu os dados da sua conta corretamente nas propriedades do componente RemoteRendering. Caso contrário, aparecerá uma mensagem na janela da consola mostrando o ID da sessão que lhe foi atribuído, e afirmando que a sessão está atualmente no estado *inicial:*

![Saída inicial da sessão](media/create-session-output.png)

Neste momento, o Azure está a providenciar um servidor para si e a iniciar uma máquina virtual de renderização remota. Isto normalmente **demora 3 a 5 minutos.** Quando o VM estiver pronto, a chamada do nosso script de Unidade `OnSessionStatusChanged` é executada e imprimirá o novo estado de sessão:

![Saída pronta para sessão](media/create-session-output-2.png)

É isso! Por enquanto, nada mais vai acontecer. Para evitar cargas, deve sempre parar as sessões quando já não são necessárias. Nesta amostra, pode fazê-lo clicando no botão **'Sessão de paragem'** ou interrompendo a simulação Unidade. Devido à propriedade **Autosstop Session** no componente *ARRServiceUnity,* que está ligado por padrão, a sessão será interrompida automaticamente para si. Se tudo falhar, devido a falhas ou problemas de ligação, a sessão poderá decorrer enquanto o seu *MaxLeaseTime* for desligado pelo servidor.

> [!NOTE]
> Parar uma sessão terá efeito imediato e não pode ser desfeito. Uma vez parado, tem que criar uma nova sessão, com a mesma sobrecarga de arranque.

## <a name="reusing-sessions"></a>Sessões de reutilização

A criação de uma nova sessão é, infelizmente, uma operação morosa. Portanto, deve-se tentar criar sessões raramente, e reutilizá-las sempre que possível.

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
> Antes de executar este código, certifique-se de desativar a **sessão de paragem automática** de opção no componente RemoteRendering. Caso contrário, todas as sessões que criar serão automaticamente interrompidas quando parar a simulação e tentar reutilizá-la falhará.

Quando premir *Play,* tem agora três botões no viewport: **Criar Sessão,** **Sessões Ativas de Consulta**e Utilizar **sessão existente**. O primeiro botão cria sempre uma nova sessão. As segundas consultas de botão que sessões *ativas* existem. Se não especificou manualmente um ID de sessão para tentar utilizar, esta ação selecionará automaticamente esse ID de sessão para utilização futura. O terceiro botão tenta ligar-se a uma sessão existente. Ou especificaste manualmente através da propriedade do componente *Session Id,* ou uma encontrada por *Consultas Ative Sessions*.

A função **AutoStartSessionAsync** é utilizada para simular as pressãos de botão fora do editor.

> [!TIP]
> É possível abrir sessões que tenham sido interrompidas, caducadas ou que estejam em estado de erro. Embora já não possam ser usados para renderização, pode consultar os seus dados, uma vez que abriu uma sessão inativa. O código acima verifica o estado de uma sessão em `ARRService_OnSessionStarted` , para parar automaticamente quando a sessão se tornar inutilizável.

Com esta funcionalidade, pode agora criar e reutilizar sessões, o que deverá melhorar significativamente o seu fluxo de trabalho de desenvolvimento.

Normalmente, a criação de uma sessão seria desencadeada fora da aplicação do cliente devido ao tempo necessário para girar o servidor.

## <a name="connect-to-an-active-session"></a>Conecte-se a uma sessão ativa

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

1. **Press Play** in Unitity.
1. Abrir uma sessão:
    1. Se já tiver uma sessão, prima **Sessões Ativas de Consulta** e, em seguida, **Utilize a sessão existente**.
    1. Caso contrário, prima **Criar Sessão**.
1. Prima **Ligar**.
1. Após alguns segundos, a saída da consola deve imprimir que está ligada.
1. Por enquanto, nada mais deveria acontecer.
1. Prima **Desligar** ou parar o modo de reprodução da Unidade.

>[!NOTE]
> Vários utilizadores podem *abrir* uma sessão para consultar as suas informações, mas apenas um utilizador pode estar *ligado* a uma sessão de cada vez. Se outro utilizador já estiver ligado, a ligação falhará com um **erro de aperto de mão**.

## <a name="load-a-model"></a>Carregue um modelo

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

Quando premir agora, abra uma sessão e ligue-a, aparece o botão **Modelo de Carga.** Depois de clicar nele, a saída da consola mostrará o progresso do carregamento e quando atingir 100% deve ver o modelo de um motor aparecer:

![Modelo carregado no editor](media/model-loaded-replace-me.png)

O [WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) é um componente importante utilizado para a [estabilidade do holograma.](https://docs.microsoft.com/windows/mixed-reality/hologram-stability) No entanto, só terá efeito quando implantado num dispositivo de Realidade Mista.

> [!TIP]
> Se seguiu o [Quickstart: Converta um modelo para renderização,](../../quickstarts/convert-model.md)já sabe como converter os seus próprios modelos. Tudo o que precisa fazer agora para o renderizar, é colocar o URI num modelo convertido na propriedade *Model Name.*

## <a name="display-frame-statistics"></a>Estatísticas de quadros de exibição

A Azure Remote Rendering rastreia várias informações sobre a qualidade da ligação. Para uma forma rápida de exibir estas informações, faça o seguinte:

Crie um [novo script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) e dê-lhe o nome **RemoteFrameStats**. Abra o ficheiro de script e substitua todo o seu conteúdo pelo código abaixo:

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

Crie um GameObject e nomeie-o *FrameStats*. Prenda-o como nó de criança ao objeto *da câmara principal* e coloque a sua posição em x = **0, y = 0, z = 0,325**. Adicione o componente **RemoteFrameStats** ao objeto.

Adicione um objeto de criança **UI > Canvas** ao objeto *FrameStats* e desemprete as suas propriedades desta forma:

![propriedades de tela](media/framestats-canvas.png)

Adicione um objeto **ui > texto** como uma criança da tela e definir as suas propriedades assim:

![propriedades de texto](media/framestats-text.png)

Selecione o objeto *FrameStats* e povoe o **campo FrameStats** clicando no ícone círculo e selecionando o objeto **De texto:**

![definição de propriedade de texto](media/framestats-set-text.png)

Agora, quando ligado à sessão remota, o texto deve mostrar as estatísticas de streaming:

![saída de estatísticas de quadro](media/framestats-output.png)

O código desativa a atualização estatística fora do editor, uma vez que uma caixa de texto fechada à cabeça seria uma distração. Uma implementação mais sofisticada encontra-se no projeto [Quickstart.](../../quickstarts/render-model.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu todos os passos necessários para tomar um projeto de Unidade em branco e fazê-lo trabalhar com a Azure Remote Rendering. No próximo tutorial, vamos analisar mais de perto como trabalhar com entidades remotas.

> [!div class="nextstepaction"]
> [Tutorial: Trabalhar com entidades remotas na Unidade](working-with-remote-entities.md)
