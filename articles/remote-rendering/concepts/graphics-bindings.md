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
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681886"
---
# <a name="graphics-binding"></a>Ligação gráfica

Para poder utilizar a Renderização Remota Azure numa aplicação personalizada, tem de ser integrada no pipeline de renderização da aplicação. Esta integração é da responsabilidade da ligação gráfica.

Uma vez configurada, a ligação gráfica dá acesso a várias funções que afetam a imagem renderizada. Estas funções podem ser separadas em duas categorias: funções gerais que estão `Microsoft.Azure.RemoteRendering.GraphicsApiType`sempre disponíveis e funções específicas que só são relevantes para os selecionados .

## <a name="graphics-binding-in-unity"></a>Ligação gráfica em Unidade

Em Unidade, toda a ligação é tratada pela `RemoteUnityClientInit` estrutura passada para `RemoteManagerUnity.InitializeManager`. Para definir o modo `GraphicsApiType` gráfico, o campo tem de ser definido para a ligação escolhida. O campo será automaticamente povoado dependendo da presença de um XRDevice. O comportamento pode ser manualmente ultrapassado com os seguintes comportamentos:

* **HoloLens 2**: a ligação gráfica [da Realidade Mista do Windows](#windows-mixed-reality) é sempre utilizada.
* **Aplicação de ambiente de trabalho UWP plana**: [A simulação](#simulation) é sempre usada. Para utilizar este modo, certifique-se de seguir os passos no [Tutorial: Configurar um projeto de Unidade do zero](../tutorials/unity/project-setup.md).
* **Editor de unidade**: [A simulação](#simulation) é sempre usada a menos que um auricular WMR VR esteja ligado, caso em que a ARR será desativada para permitir depurar as partes não relacionadas com o ARR da aplicação. Ver também [remografia holográfica.](../how-tos/unity/holographic-remoting.md)

A única outra parte relevante para a Unidade é o acesso à [ligação básica,](#access)todas as outras secções abaixo podem ser ignoradas.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuração de ligação gráfica em aplicações personalizadas

Para selecionar uma ligação gráfica, dê os seguintes dois passos: Primeiro, a ligação gráfica tem de ser inicializada estáticaquando o programa é inicializado:

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

A chamada acima é necessária para inicializar a renderização remota azure nas APIs holográficas. Esta função deve ser chamada antes de qualquer API holográfico ser chamada e antes de qualquer outra APIs de renderização remota ser acedida. Da mesma forma, a função `RemoteManagerStatic.ShutdownRemoteRendering();` de desinite correspondente deve ser chamada depois de já não serem chamadas APIs holográficas.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acesso à ligação gráfica

Uma vez configurado um cliente, a ligação gráfica `AzureSession.GraphicsBinding` básica pode ser acedida com o getter. Como exemplo, as estatísticas do último quadro podem ser recuperadas desta forma:

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>APIs gráficos

Existem atualmente duas APIs gráficas `WmrD3D11` que `SimD3D11`podem ser selecionadas, e . Um terceiro `Headless` existe, mas ainda não é apoiado do lado do cliente.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`é a ligação padrão para executar em HoloLens 2. Vai criar `GraphicsBindingWmrD3d11` a ligação. Neste modo, os ganchos de renderização remota Azure diretamente nas APIs holográficas.

Para aceder às ligações gráficas `GraphicsBinding` derivadas, a base tem de ser lançada.
Há duas coisas que precisam de ser feitas para usar a ligação WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informar a renderização remota do sistema de coordenadas usado

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

Quando o `ptr` acima deve ser `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` um indicador de um objeto nativo que define o sistema de coordenadas espaciais mundiais no qual as coordenadas na API são expressas.

#### <a name="render-remote-image"></a>Render imagem remota

No início de cada quadro, o quadro remoto precisa de ser transformado no tampão traseiro. Isto é feito `BlitRemoteFrame`através da chamada , que preencherá informações de cor e profundidade no alvo de renderização atualmente ligado. Assim, é importante que isto seja feito depois de vincular o tampão traseiro como alvo de renderização.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>Simulação

`GraphicsApiType.SimD3D11`é a ligação de simulação e se selecionada cria a `GraphicsBindingSimD3d11` ligação gráfica. Esta interface é usada para simular o movimento da cabeça, por exemplo, numa aplicação de ambiente de trabalho e torna uma imagem monoscópica.
A configuração é um pouco mais envolvida e funciona da seguinte forma:

#### <a name="create-proxy-render-target"></a>Criar alvo de renderização proxy

O conteúdo remoto e local precisa de ser renderizado para um alvo de renderização `GraphicsBindingSimD3d11.Update` de cor/profundidade offscreen chamado 'proxy' utilizando os dados da câmara proxy fornecidos pela função. O representante deve corresponder à resolução do tampão traseiro. Uma vez que `GraphicsBindingSimD3d11.InitSimulation` uma sessão esteja pronta, precisa ser chamada antes de se ligar a ela:

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

A função init precisa de ser fornecida com indicações para o dispositivo d3d nativo, bem como para a textura de cor e profundidade do alvo de representação proxy. Uma vez `AzureSession.ConnectToRuntime` inicializado, `DisconnectFromRuntime` e pode ser chamado várias `GraphicsBindingSimD3d11.DeinitSimulation` vezes, mas quando mudar `GraphicsBindingSimD3d11.InitSimulation` para uma sessão diferente, precisa ser chamado primeiro na sessão antiga antes pode ser chamado em outra sessão.

#### <a name="render-loop-update"></a>Rendertualização de loop

A atualização do loop render izado consiste em múltiplas etapas:

1. Cada quadro, antes de qualquer `GraphicsBindingSimD3d11.Update` renderização, é chamado com a atual transformação da câmara que é enviada para o servidor para ser renderizada. Ao mesmo tempo, a transformação de procuração devolvida deve ser aplicada à câmara de procuração para render no alvo de representação proxy.
Se a atualização `SimulationUpdate.frameId` de procuração devolvida for nula, ainda não existem dados remotos. Neste caso, em vez de renderizar no alvo de prestação de procuração, qualquer conteúdo local deve ser entregue diretamente ao tampão traseiro utilizando os dados atuais da câmara e os próximos dois passos são ignorados.
1. O pedido deve agora ligar o `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`alvo de prestação de procuração e chamar . Isto preencherá a cor remota e a informação de profundidade no alvo de representação. Qualquer conteúdo local pode agora ser entregue no proxy utilizando a transformação da câmara proxy.
1. Em seguida, o tampão traseiro precisa `GraphicsBindingSimD3d11.ReprojectProxy` ser amarrado como um alvo de renderização e chamado em que ponto o tampão traseiro pode ser apresentado.

``` cs
AzureSession currentSesson = ...;
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

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Criação de um projeto de unidade do zero](../tutorials/unity/project-setup.md)
