---
title: Integrar renderização remota numa aplicação holográfica C++/DirectX11
description: Explica como integrar a renderização remota numa simples App Holográfica C++/DirectX11 criada com o assistente de projeto visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: a07a8a9c50e0f71daa48f65ebf8c2e7a7f166cc5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594304"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Tutorial: Integrar renderização remota numa app holográfica HoloLens

Neste tutorial, vai aprender:

> [!div class="checklist"]
>
> * Usando o Visual Studio para criar uma App Holográfica que pode ser implementada para HoloLens
> * Adicione os snippets de código necessários e as definições de projeto para combinar renderização local com conteúdo prestado remotamente

Este tutorial centra-se em adicionar as partes necessárias a uma amostra nativa `Holographic App` para combinar renderização local com renderização remota Azure. O único tipo de feedback de estado nesta aplicação é através do painel de saída de depuração dentro do Visual Studio, pelo que é recomendado iniciar a amostra a partir de inside Visual Studio. Adicionar feedback adequado na aplicação está fora do âmbito desta amostra, porque construir um painel de texto dinâmico de raiz envolve muito código. Um bom ponto de partida é a classe `StatusDisplay` , que faz parte do projeto de amostra do [Remoting Player no GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Na verdade, a versão pré-enlatada deste tutorial usa uma cópia local dessa classe.

> [!TIP]
> O [repositório de amostras de ARR](https://github.com/Azure/azure-remote-rendering) contém o resultado deste tutorial como um projeto do Estúdio Visual que está pronto a usar. É também enriquecido com erros e relatórios de estado adequados através da classe UI `StatusDisplay` . Dentro do tutorial, todas as adições específicas do ARR são miradas `#ifdef USE_REMOTE_RENDERING`  /  `#endif` por, por isso é fácil identificar as adições de renderização remota.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial você precisa:

* Informações da sua conta (ID de conta, chave de conta, domínio de conta, ID de subscrição). Se não tiver uma conta, [crie uma conta.](../../../how-tos/create-an-account.md)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A versão mais recente do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/).
* [Ferramentas de Estúdio Visual para Realidade Mista.](/windows/mixed-reality/install-the-tools) Especificamente, as seguintes instalações *de carga de trabalho* são obrigatórias:
  * **Desenvolvimento de desktop com C++**
  * **Desenvolvimento da Plataforma Universal windows (UWP)**
* Os modelos de aplicativo de realidade mista do Windows para estúdio visual [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Criar uma nova amostra de Aplicação Holográfica

Como primeiro passo, criamos uma amostra de stock que é a base para a integração de renderização remota. Abra o Estúdio Visual e selecione "Criar um novo projeto" e procure "Holographic DirectX 11 App (Universal Windows) (C++/WinRT)"

![Criar novo projeto](media/new-project-wizard.png)

Digite um nome de projeto à sua escolha, escolha um caminho e selecione o botão "Criar".
No novo projeto, altere a configuração para **"Debug/ARM64".** Deverá agora ser capaz de compilá-lo e implantá-lo num dispositivo HoloLens 2 ligado. Se o fizeres no HoloLens, devias ver um cubo rotativo à tua frente.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Adicionar dependências de renderização remota através do NuGet

O primeiro passo para adicionar capacidades de renderização remota é adicionar as dependências do lado do cliente. Dependências relevantes estão disponíveis como um pacote NuGet.
No Solution Explorer, clique com o botão direito no projeto e selecione **"Gerir pacotes NuGet..."** a partir do menu de contexto.

No diálogo solicitado, consulte o pacote NuGet denominado **"Microsoft.Azure.RemoteRendering.Cpp"**:

![Navegue para pacote NuGet](media/add-nuget.png)

e adicione-o ao projeto selecionando a embalagem e, em seguida, pressionando o botão "Instalar".

O pacote NuGet adiciona as dependências de renderização remota ao projeto. Especificamente:
* Ligação contra a biblioteca do cliente (RemoteRenderingClient.lib).
* Estabeleça as dependências .dll.
* Desacorda o caminho correto para o diretório incluído.

## <a name="project-preparation"></a>Preparação do projeto

Precisamos de fazer pequenas alterações ao projeto existente. Estas mudanças são subtis, mas sem elas a renderização remota não funcionaria.

### <a name="enable-multithread-protection-on-directx-device"></a>Ativar a proteção multi-lirísta no dispositivo DirectX
O `DirectX11` aparelho deve ter uma proteção multi-televisão ativada. Para alterar isso, abra o ficheiro DeviceResources.cpp na pasta "Common", e insira o seguinte código no final da `DeviceResources::CreateDeviceResources()` função:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Ativar as capacidades de rede no manifesto da aplicação
As capacidades de rede devem ser explicitamente ativadas para a aplicação implementada. Sem que isto seja configurado, as consultas de ligação resultarão eventualmente em intervalos de tempo. Para ativar, clique duas vezes no `package.appxmanifest` item no explorador de solução. Na próxima UI, vá ao separador **Capabilities** e selecione:
* Internet (Servidor & cliente)
* Internet (Cliente)

![Capacidades de rede](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrar renderização remota

Agora que o projeto está preparado, podemos começar com o código. Um bom ponto de entrada na aplicação é a classe `HolographicAppMain` (arquivo HolographicAppMain.h/cpp) porque tem todos os ganchos necessários para a inicialização, desinexminagem e renderização.

### <a name="includes"></a>Inclusões

Começamos por adicionar as incluições necessárias. Adicione o seguinte incluir para arquivar HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

... e estas diretivas adicionais `include` para arquivar HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

Para a simplicidade do código, definimos o seguinte atalho de espaço de nome no topo do ficheiro HolographicAppMain.h, após as `include` diretivas:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Este atalho é útil para que não tenhamos que escrever o espaço completo de nomes em todos os lugares, mas ainda podemos reconhecer estruturas de dados específicas do ARR. Naturalmente, também poderíamos utilizar a `using namespace...` diretiva.

### <a name="remote-rendering-initialization"></a>Inicialização de renderização remota
 
Precisamos de guardar alguns objetos para a sessão, etc. durante o período de vida da aplicação. A vida útil coincide com a vida útil do objeto da `HolographicAppMain` aplicação, por isso adicionamos os nossos objetos como membros à `HolographicAppMain` classe. O próximo passo é adicionar os seguintes membros da classe no ficheiro HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::RemoteRenderingClient> m_client;  // the client instance
    RR::ApiHandle<RR::RenderingSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RenderingConnection> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Connection()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Um bom lugar para fazer a implementação real é o construtor de classe `HolographicAppMain` . Temos que fazer três tipos de inicialização lá:
1. A inicialização única do sistema de renderização remota
1. Criação de clientes (autenticação)
1. Criação de sessão

Fazemos tudo isso sequencialmente no construtor. No entanto, em casos de utilização real, seria conveniente fazer estas etapas separadamente.

Adicione o seguinte código ao início do corpo do construtor em ficheiro HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.ConnectionType = RR::ConnectionType::General;
        clientInit.GraphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.ToolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.UnitsPerMeter = 1.0f;
        clientInit.Forward = RR::Axis::NegativeZ;
        clientInit.Right = RR::Axis::X;
        clientInit.Up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create Client
    {
        // Users need to fill out the following with their account data and model
        RR::SessionConfiguration init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
        m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
    }

    // 3. Open/create rendering session
    {
        auto SessionHandler = [&](RR::Status status, RR::ApiHandle<RR::CreateRenderingSessionResult> result)
        {
            if (status == RR::Status::OK)
            {
                auto ctx = result->GetContext();
                if (ctx.Result == RR::Result::Success)
                {
                    SetNewSession(result->GetSession());
                }
                else
                {
                    SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                }
            }
            else
            {
                SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
            }
        };

        // If we had an old (valid) session that we can recycle, we call async function m_client->OpenRenderingSessionAsync
        if (!m_sessionOverride.empty())
        {
            m_client->OpenRenderingSessionAsync(m_sessionOverride, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
        else
        {
            // create a new session
            RR::RenderingSessionCreationOptions init;
            init.MaxLeaseInMinutes = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            m_client->CreateNewRenderingSessionAsync(init, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

O código chama funções de membro `SetNewSession` e `SetNewState` , que implementaremos no parágrafo seguinte juntamente com o resto do código da máquina estatal.

Note que as credenciais são codificadas na amostra e que precisam de ser preenchidas no lugar[(ID de conta, chave de conta, domínio de conta,](../../../how-tos/create-an-account.md#retrieve-the-account-information)e [domínio de renderização remota).](../../../reference/regions.md)

Fazemos a des-inicialização simetricamente e em ordem inversa no final do corpo destrutor:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->Disconnect();
        m_session = nullptr;
    }

    // Destroy front end:
    m_client = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Máquina do Estado

Na renderização remota, as funções-chave para criar uma sessão e carregar um modelo são funções assíncronos. Para responder a isto, precisamos de uma máquina estatal simples que essencialmente transite pelos seguintes Estados automaticamente:

*Inicialização -> Sessão criação -> Session a partir de -> Carregamento de modelo (com progresso)*

Assim, como próximo passo, adicionamos um pouco de manuseamento de máquinas estatais à classe. Declaramos o nosso próprio enum `AppConnectionStatus` para os vários Estados em que a nossa candidatura pode ser apresentada. É semelhante a `RR::ConnectionStatus` , mas tem um estado adicional para ligação falhada.

Adicione os seguintes membros e funções à declaração de classe:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

Do lado da implementação no ficheiro .cpp, adicione estes organismos de função:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSasOptions options;
    options.ModelUri = m_modelURI.c_str();
    options.Parent = nullptr;

    // start the async model loading
    m_api->LoadModelFromSasAsync(options,
        // completed callback
        [this](RR::Status status, RR::ApiHandle<RR::LoadModelResult> result)
        {
            m_modelLoadResult = RR::StatusToResult(status);
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
        },
        // progress update callback
            [this](float progress)
        {
            // progress callback
            m_modelLoadingProgress = progress;
            m_needsStatusUpdate = true;
        });
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_sessionStartingTime = m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Connection();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>Por atualização de quadro

Temos de atualizar o cliente uma vez por simulação e fazer algumas atualizações adicionais do estado. A `HolographicAppMain::Update` função fornece um bom gancho para atualizações por quadro.

#### <a name="state-machine-update"></a>Atualização da máquina do estado

Precisamos de sondar o estado da sessão e ver se ela passou para `Ready` o Estado. Se tivermos uma ligação com sucesso, finalmente iniciamos o carregamento do modelo via `StartModelLoading` .

Adicione o seguinte código ao corpo de `HolographicAppMain::Update` função:

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                m_session->GetPropertiesAsync([this](RR::Status status, RR::ApiHandle<RR::RenderingSessionPropertiesResult> propertiesResult)
                    {
                        if (status == RR::Status::OK)
                        {
                            auto ctx = propertiesResult->GetContext();
                            if (ctx.Result == RR::Result::Success)
                            {
                                auto res = propertiesResult->GetSessionProperties();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following ConnectAsync is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::RendererInitOptions init;
                                    init.IgnoreCertificateValidation = false;
                                    init.RenderMode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectAsync(init, [](RR::Status, RR::ConnectionStatus) {});
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                            }
                        }
                        else
                        {
                            SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                        }
                        m_sessionPropertiesQueryInProgress = false; // next try
                    });                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Coordenar atualização do sistema

Temos de concordar com o serviço de renderização de um sistema de coordenadas a utilizar. Para aceder ao sistema de coordenadas que queremos utilizar, precisamos do `m_stationaryReferenceFrame` que é criado no final da função. `HolographicAppMain::OnHolographicDisplayIsAvailableChanged`

Este sistema de coordenadas geralmente não muda, por isso esta é uma inicialização única. Deve ser chamado novamente se a sua aplicação alterar o sistema de coordenadas.

O código acima define o sistema de coordenadas uma vez dentro da `Update` função assim que ambos temos um sistema de coordenadas de referência e uma sessão conectada.

#### <a name="camera-update"></a>Atualização da câmara

Temos de atualizar os planos de clipe da câmara para que a câmara do servidor seja mantida em sintonia com a câmara local. Podemos fazê-lo no final da `Update` função:

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Composição

A última coisa a fazer é invocar a renderização do conteúdo remoto. Temos de fazer esta chamada na posição exata dentro do gasoduto de renderização, depois do alvo de renderização ser claro e definir o viewport. Insira o seguinte corte na função interior do `UseHolographicCameraResources` `HolographicAppMain::Render` bloqueio:

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Existing check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Executar o exemplo

A amostra deve agora estar num estado em que compila e corre.

Quando a amostra funciona corretamente, mostra o cubo rotativo mesmo à sua frente, e depois de alguma sessão de criação e carregamento de modelos, torna o modelo do motor localizado na posição atual da cabeça. A criação de sessão e o carregamento de modelos podem demorar até alguns minutos. O estado atual é apenas escrito para o painel de saída do Visual Studio. Assim, recomenda-se iniciar a amostra a partir do Interior do Estúdio Visual.

> [!CAUTION]
> O cliente desliga-se do servidor quando a função de marcação não é chamada durante alguns segundos. Assim, desencadear pontos de rutura pode facilmente fazer com que a aplicação se desligue.

Para visualizar o estado adequado com um painel de texto, consulte a versão pré-enlatada deste tutorial no GitHub.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu todos os passos necessários para adicionar renderização remota a uma amostra **de app holográfica** de stock C++/DirectX11.
Para converter o seu próprio modelo, consulte o seguinte quickstart:

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para composição](../../../quickstarts/convert-model.md)