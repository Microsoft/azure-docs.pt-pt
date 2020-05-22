---
title: Integrar a renderização remota numa aplicação holográfica C++/DirectX11
description: Explica como integrar a Renderização Remota numa simples Aplicação Holográfica C++/DirectX11 criada com o assistente do projeto Visual Studio
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776886"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Tutorial: Integrar a renderização remota numa aplicação holográfica HoloLens

Neste tutorial, vai aprender:

> [!div class="checklist"]
>
> * Usando o Estúdio Visual para criar uma App Holográfica que pode ser implementada para HoloLens
> * Adicione os códigos necessários e as definições do projeto para combinar renderização local com conteúdo tornado remotamente

Este tutorial centra-se em adicionar as partes necessárias a uma amostra nativa `Holographic App` para combinar renderização local com renderização remota Azure. O único tipo de feedback de estado nesta aplicação é através do painel de saída de depuração dentro do Visual Studio, pelo que é aconselhável iniciar a amostra a partir do Estúdio Visual. A adição de feedback adequado na aplicação está fora do âmbito desta amostra, porque construir um painel de texto dinâmico de raiz envolve muita codificação. Um bom ponto de partida é a classe, que faz parte do projeto de amostra do `StatusDisplay` [Jogador Remoting no GitHub.](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content) Na verdade, a versão pré-enlatada deste tutorial usa uma cópia local dessa classe.

> [!TIP]
> O [repositório](https://github.com/Azure/azure-remote-rendering) de amostras ARR contém o resultado deste tutorial como um projeto do Estúdio Visual que está pronto a ser usado. É também enriquecido com um erro adequado e um relatório de estado através da classe `StatusDisplay` UI. Dentro do tutorial, todas as adições específicas de ARR são reparadas `#ifdef USE_REMOTE_RENDERING`  /  `#endif` por, por isso é fácil identificar as adições de Renderização Remota.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial você precisa:

* Informação da sua conta (ID da conta, chave de conta, ID de subscrição). Se não tem uma conta, [crie uma conta.](../../../how-tos/create-an-account.md)
* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A versão mais recente do Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/).
* Os modelos de aplicações de realidade mista do Windows para estúdio visual [(download)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Crie uma nova amostra de App Holográfica

Como primeiro passo, criamos uma amostra de stock que é a base para a integração da Renderização Remota. Open Visual Studio e selecione "Create a new project" e procure "Holographic DirectX 11 App (Universal Windows) (C++/WinRT)"

![Criar novo projeto](media/new-project-wizard.png)

Digite um nome de projeto à sua escolha, escolha um caminho e selecione o botão "Criar".
No novo projeto, altere a configuração para **"Debug / ARM64".** Agora deve ser capaz de compilar e implantá-lo num dispositivo HoloLens 2 ligado. Se o publicares no HoloLens, devias ver um cubo rotativo à tua frente.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Adicione dependências de renderização remota através do NuGet

O primeiro passo para adicionar capacidades de renderização remota é adicionar as dependências do lado do cliente. Dependências relevantes estão disponíveis como um pacote NuGet.
No Solution Explorer, clique no direito do projeto e selecione **"Manage NuGet Packages..."** do menu de contexto.

No diálogo solicitado, procure o pacote NuGet denominado **"Microsoft.Azure.RemoteRendering.Cpp":**

![Procure o pacote NuGet](media/add-nuget.png)

e adicioná-lo ao projeto selecionando a embalagem e, em seguida, premindo o botão "Instalar".

O pacote NuGet adiciona as dependências de renderização remota ao projeto. Mais concretamente:
* Link contra a biblioteca do cliente (RemoteRenderingClient.lib).
* Instale as dependências .dll.
* Desloque o caminho correto para o diretório incluído.

## <a name="project-preparation"></a>Preparação do projeto

Precisamos de fazer pequenas alterações ao projeto existente. Estas mudanças são subtis, mas sem elas a Renderização Remota não funcionaria.

### <a name="enable-multithread-protection-on-directx-device"></a>Ativar a proteção multithread no dispositivo DirectX
O `DirectX11` aparelho deve ter uma proteção multifios ativada. Para alterar isso, abra o ficheiro DeviceResources.cpp na pasta "Common", e insira o seguinte código no final da `DeviceResources::CreateDeviceResources()` função:

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Ativar as capacidades de rede no manifesto da aplicação
As capacidades de rede devem ser ativadas explicitamente para a aplicação implementada. Sem que isto seja configurado, as consultas de ligação resultarão eventualmente em intervalos. Para ativar, clique duas vezes no `package.appxmanifest` item no explorador de soluções. No próximo UI, vá ao separador **Capabilities** e selecione:
* Internet (Servidor de & cliente)
* Internet (Cliente)

![Capacidades de rede](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrar renderização remota

Agora que o projeto está preparado, podemos começar com o código. Um bom ponto de entrada na aplicação é a classe `HolographicAppMain` (ficheiro HolographicAppMain.h/cpp) porque tem todos os ganchos necessários para inicialização, desinicialização e renderização.

### <a name="includes"></a>Inclusões

Começamos por adicionar os incluimentos necessários. Adicione o seguinte inclua o ficheiro HolographicAppMain.h:

```cpp
#include <AzureRemoteRendering.h>
```

... e esta diretiva adicional `include` para arquivar HolographicAppMain.cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

Para a simplicidade do código, definimos o seguinte atalho de espaço de nome no topo do ficheiro HolographicAppMain.h, após a `include` diretiva:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Este atalho é útil para que não tenhamos que escrever o espaço completo de nomes em todo o lado, mas ainda podemos reconhecer estruturas de dados específicas do ARR. É claro que também poderíamos utilizar a `using namespace...` diretiva.

### <a name="remote-rendering-initialization"></a>Inicialização de renderização remota
 
Precisamos de segurar alguns objetos para a sessão, etc. durante a vida útil da aplicação. A vida inteira coincide com a vida útil do objeto da `HolographicAppMain` aplicação, por isso adicionamos os nossos objetos como membros à `HolographicAppMain` aula. O próximo passo é adicionar os seguintes membros da classe no ficheiro HolographicAppMain.h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

Um bom lugar para fazer a implementação real é o construtor de `HolographicAppMain` classe. Temos que fazer três tipos de inicialização lá:
1. A inicialização única do sistema de renderização remota
1. Criação frontal
1. Criação de sessões

Fazemos tudo isso sequencialmente no construtor. No entanto, em casos de utilização real, seria apropriado fazer estas etapas separadamente.

Adicione o seguinte código ao início do corpo de construtores em ficheiro HolographicAppMain.cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
Dentro da função `SetNewSession` local, registamo-nos num callback que é desencadeado sempre que o estado de ligação na determinada sessão muda. Redirecionamos essa chamada para a nossa própria `OnConnectionStatusChanged` função. Vamos declará-lo e implementá-lo (e o resto do código de máquina do Estado) no parágrafo seguinte. Note também que as credenciais estão codificadas na amostra e precisam de ser preenchidas no local[(ID de conta, chave](../../../how-tos/create-an-account.md#retrieve-the-account-information)de conta, e [domínio).](../../../reference/regions.md)

Fazemos a desinicialização simetricamente e em ordem inversa no final do corpo de structor:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Máquina do Estado

Na renderização remota, as funções-chave para criar uma sessão e carregar um modelo são funções assíncronas. Para ter em conta isto, precisamos de uma máquina de Estado simples que essencialmente transite através dos seguintes Estados automaticamente:

*Inicialização -> Sessão criação -> carregamento de modelos (com progresso)*

Assim, como próximo passo, adicionamos um pouco de manipulação de máquinas estatais à classe. Declaramos o nosso próprio enum `AppConnectionStatus` para os vários estados em que a nossa aplicação pode estar. É semelhante `RR::ConnectionStatus` a, mas tem um estado adicional para a ligação falhada.

Adicione os seguintes membros e funções à declaração de classe:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
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
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

Do lado da implementação no ficheiro .cpp, adicione estes organismos de função:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
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

Temos que marcar o cliente uma vez por simulação. A classe `HolographicApp1Main` fornece um bom gancho para atualizações por quadro, por isso adicione o seguinte código ao corpo de função: `HolographicApp1Main::Update`

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Composição

A última coisa a fazer é invocar a renderização do conteúdo remoto. Temos de fazer esta chamada na posição exata dentro do gasoduto de renderização, depois do alvo de renderização estar claro. Insira o seguinte corte na `UseHolographicCameraResources` função interior: `HolographicAppMain::Render`

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Executar o exemplo

A amostra deve agora estar num estado em que compila e corre.

Quando a amostra funciona corretamente, mostra o cubo rotativo mesmo à sua frente, e após alguma criação de sessão e carregamento de modelos, torna o modelo do motor localizado na posição atual da cabeça. A criação de sessões e o carregamento do modelo podem demorar até alguns minutos. O estado atual só está escrito para o painel de saída do Estúdio Visual. Assim, recomenda-se iniciar a amostra a partir do Estúdio Visual.

> [!CAUTION]
> O cliente desliga-se do servidor quando a função de carrapato não é chamada durante alguns segundos. Assim, desencadear pontos de rutura pode facilmente fazer com que a aplicação se desconecte.

Para um ecrã de estado adequado com um painel de texto, consulte a versão pré-enlatada deste tutorial no GitHub.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu todos os passos necessários para adicionar a Renderização Remota a uma amostra de **app holográfica** c++/DirectX11.
Para converter o seu próprio modelo, consulte o seguinte arranque rápido:

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](../../../quickstarts/convert-model.md)
