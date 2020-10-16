---
title: Ligação gráfica
description: Configuração de encadernações gráficas e casos de utilização
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 332213adf64e17c0935ddf612acac5bbca413a87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802298"
---
# <a name="graphics-binding"></a>Ligação gráfica

Para poder utilizar a Renderização Remota Azure numa aplicação personalizada, tem de ser integrada no pipeline de renderização da aplicação. Esta integração é da responsabilidade da ligação gráfica.

Uma vez configurado, a ligação gráfica dá acesso a várias funções que afetam a imagem renderizada. Estas funções podem ser separadas em duas categorias: funções gerais sempre disponíveis e funções específicas que só são relevantes para os selecionados `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Ligação gráfica na Unidade

Em Unidade, toda a ligação é tratada pela `RemoteUnityClientInit` estrutura passada em `RemoteManagerUnity.InitializeManager` . Para definir o modo gráfico, o `GraphicsApiType` campo tem de ser definido para a ligação escolhida. O campo será automaticamente povoado dependendo se um XRDevice está presente. O comportamento pode ser manualmente ultrapassado com os seguintes comportamentos:

* **HoloLens 2**: é sempre utilizada a ligação com gráficos [de realidade mista do Windows.](#windows-mixed-reality)
* **Aplicativo de ambiente de trabalho UWP plano**: [A simulação](#simulation) é sempre usada.
* **Editor de unidade**: [A simulação](#simulation) é sempre utilizada a menos que um auricular WMR VR esteja ligado, caso em que o ARR será desativado para permitir depurar as partes não relacionadas com ARR da aplicação. Consulte também [a remoing holográfico.](../how-tos/unity/holographic-remoting.md)

A única outra parte relevante para a Unidade é o acesso à [ligação básica,](#access)todas as outras secções abaixo podem ser ignoradas.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuração de ligação de gráficos em aplicações personalizadas

Para selecionar uma ligação gráfica, dê os seguintes dois passos: Primeiro, a ligação gráfica tem de ser inicializada estática quando o programa é inicializado:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

A chamada acima é necessária para inicializar a renderização remota do Azure nas APIs holográficas. Esta função deve ser chamada antes de qualquer API holográfica ser chamada e antes de qualquer outra APIs de renderização remota ser acedida. Da mesma forma, a função de desativação correspondente `RemoteManagerStatic.ShutdownRemoteRendering();` deve ser chamada depois de já não serem chamadas APIs holográficas.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acesso à ligação de gráficos

Uma vez configurado um cliente, a ligação gráfica básica pode ser acedida com o `AzureSession.GraphicsBinding` getter. Como exemplo, as últimas estatísticas de quadros podem ser recuperadas desta forma:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>APIs gráficos

Existem atualmente duas APIs gráficos que podem ser selecionadas, `WmrD3D11` e `SimD3D11` . Um terceiro `Headless` existe, mas ainda não é apoiado do lado do cliente.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` é a ligação padrão para executar em HoloLens 2. Vai criar a `GraphicsBindingWmrD3d11` ligação. Neste modo, a Azure Remote Rendering liga-se diretamente às APIs holográficas.

Para aceder às ligações gráficas derivadas, a base `GraphicsBinding` tem de ser lançada.
Há duas coisas que precisam de ser feitas para utilizar a ligação WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informe a renderização remota do sistema de coordenadas utilizado

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Quando o acima `ptr` deve ser um ponteiro para um objeto nativo `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` que define o sistema de coordenadas espaciais do mundo no qual as coordenadas na API são expressas.

#### <a name="render-remote-image"></a>Render imagem remota

No início de cada armação, a estrutura remota precisa de ser transformada no tampão traseiro. Isto é feito chamando `BlitRemoteFrame` , que irá preencher informações de cor e profundidade para ambos os olhos no alvo de renderização atualmente vinculado. Assim, é importante fazê-lo depois de encadernar o tampão traseiro completo como alvo de renderização.

> [!WARNING]
> Após a imagem remota ter sido cortada no backbuffer, o conteúdo local deve ser prestado utilizando uma técnica de renderização estéreo de passe único, por exemplo, utilizando **SV_RenderTargetArrayIndex**. A utilização de outras técnicas de renderização estéreo, como a renderização de cada olho num passe separado, pode resultar em degradação de desempenho ou artefactos gráficos importantes e deve ser evitada.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulação

`GraphicsApiType.SimD3D11` é a ligação de simulação e, se selecionada, cria a `GraphicsBindingSimD3d11` ligação gráfica. Esta interface é usada para simular o movimento da cabeça, por exemplo, numa aplicação de ambiente de trabalho e torna uma imagem monoscópica.

Para implementar a ligação de simulação, é importante entender a diferença entre a câmara local e o quadro remoto, conforme descrito na página da [câmara.](../overview/features/camera.md)

São necessárias duas câmaras:

* **Câmara local**: Esta câmara representa a posição atual da câmara que é impulsionada pela lógica da aplicação.
* **Proxy camera**: Esta câmara corresponde à atual *moldura remota* que foi enviada pelo servidor. Como há um atraso de tempo entre o cliente que solicita uma moldura e a sua chegada, o *Quadro Remoto* está sempre um pouco atrás do movimento da câmara local.

A abordagem básica aqui é que tanto a imagem remota como o conteúdo local são transformados num alvo fora do ecrã usando a câmara proxy. A imagem proxy é então reprojectada para o espaço da câmara local, o que é explicado mais tarde na [reprojecção do estágio](../overview/features/late-stage-reprojection.md)tardio .

A configuração é um pouco mais envolvida e funciona da seguinte forma:

#### <a name="create-proxy-render-target"></a>Criar alvo de renderização de procuração

O conteúdo remoto e local precisa de ser tornado num alvo de renderização de cor/profundidade fora do ecrã chamado 'proxy' utilizando os dados da câmara proxy fornecidos pela `GraphicsBindingSimD3d11.Update` função.

O representante deve corresponder à resolução do tampão traseiro e deve estar int no *formato DXGI_FORMAT_R8G8B8A8_UNORM* ou *DXGI_FORMAT_B8G8R8A8_UNORM.* Uma vez que uma sessão esteja pronta, `GraphicsBindingSimD3d11.InitSimulation` precisa ser chamado antes de ligar a ela:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

A função init deve ser fornecida com ponteiros para o dispositivo d3d nativo, bem como para a textura de cor e profundidade do alvo de renderização proxy. Uma vez inicializado, `AzureSession.ConnectToRuntime` e pode ser chamado `DisconnectFromRuntime` várias vezes, mas quando se muda para uma sessão diferente, `GraphicsBindingSimD3d11.DeinitSimulation` precisa ser chamado primeiro na sessão antiga antes `GraphicsBindingSimD3d11.InitSimulation` pode ser convocado em outra sessão.

#### <a name="render-loop-update"></a>Atualização do loop de renderização

A atualização do loop de renderização consiste em vários passos:

1. Cada frame, antes de qualquer renderização, `GraphicsBindingSimD3d11.Update` é chamada com a transformação da câmara atual que é enviada para o servidor a ser renderizada. Ao mesmo tempo, a transformação de procuração devolvida deve ser aplicada à câmara proxy para renderizar o alvo de renderização.
Se a atualização de procuração devolvida `SimulationUpdate.frameId` for nula, ainda não existem dados remotos. Neste caso, em vez de renderizar o alvo de renderização proxy, qualquer conteúdo local deve ser entregue diretamente ao tampão traseiro utilizando os dados atuais da câmara e os dois passos seguintes são ignorados.
1. O pedido deve agora vincular o alvo e a chamada de renderização de procuração `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Isto preencherá a cor remota e a informação de profundidade no alvo de renderização de procuração. Qualquer conteúdo local pode agora ser entregue no proxy utilizando a transformação da câmara proxy.
1. Em seguida, o tampão traseiro precisa ser amarrado como um alvo de renderização e `GraphicsBindingSimD3d11.ReprojectProxy` chamado em que ponto o tampão traseiro pode ser apresentado.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# RemoteManagerStatic.StartupRemoteRendering()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# Aula de Gráficas](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GráficasBindingWmrD3d11 classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GráficasBindingSimD3d11 classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Estrutura de localização remota C++](https://docs.microsoft.com/cpp/api/remote-rendering/remoterenderinginitialization)
* [Classe C++ GraphicsBinding](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbinding)
* [C++ GráficasBindingWmrD3d11 classe](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GráficasBindingSimD3d11 classe](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Passos seguintes

* [Câmara](../overview/features/camera.md)
* [Reprojeção da última fase](../overview/features/late-stage-reprojection.md)
* [Tutorial: Visualização remota de modelos renderizados](../tutorials/unity/view-remote-models/view-remote-models.md)
