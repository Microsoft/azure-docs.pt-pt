---
title: Modos de composição
description: Descreve os diferentes modos de renderização do lado do servidor
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594506"
---
# <a name="rendering-modes"></a>Modos de composição

A Renderização Remota oferece dois modos principais de funcionamento, o modo **TileBasedComposition** e o modo **DepthBasedComposition.** Estes modos determinam como a carga de trabalho é distribuída por várias GPUs no servidor. O modo tem de ser especificado no tempo de ligação e não pode ser alterado durante o tempo de funcionamento.

Ambos os modos vêm com vantagens, mas também com limitações inerentes à funcionalidade, pelo que escolher o modo mais adequado é usar caso específico.

## <a name="modes"></a>Modos

Os dois modos são agora discutidos com mais pormenor.

### <a name="tilebasedcomposition-mode"></a>Modo 'TileBasedComposition'

No modo **TileBasedComposition,** todos os GPU envolvidos prestam subrectangles específicos (azulejos) no ecrã. A GPU principal compõe a imagem final dos azulejos antes de ser enviada como moldura de vídeo para o cliente. Assim, todas as GPUs requerem ter o mesmo conjunto de recursos para renderização, pelo que os ativos carregados precisam de se encaixar na memória de uma única GPU.

A qualidade de renderização neste modo é ligeiramente melhor do que no modo **DepthBasedComposition,** uma vez que a MSAA pode trabalhar no conjunto completo de geometria para cada GPU. A imagem que se segue mostra que o antialiasing funciona corretamente para ambas as bordas da mesma forma:

![MSAA em TileBasedComposição](./media/service-render-mode-quality.png)

Além disso, neste modo cada peça pode ser mudada para um material transparente ou comutada para modo **transparente** através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modo 'ProfundidadeBasedComposição'

No modo **DepthBasedComposition,** todos os GPU envolvidos prestam em resolução completa do ecrã, mas apenas um subconjunto de malhas. A composição final da imagem na GPU principal tem o cuidado de que as peças são devidamente fundidas de acordo com as suas informações de profundidade. Naturalmente, a carga útil da memória é distribuída pelas GPUs, permitindo assim a renderização de modelos que não se enquadrariam na memória de uma única GPU.

Cada GPU usa MSAA para conteúdo local antialias. No entanto, pode haver um aliasing inerente entre as bordas de GPUs distintos. Este efeito é atenuado pelo pós-processamento da imagem final, mas a qualidade da MSAA ainda é pior do que no modo **TileBasedComposition.**

Os artefactos da MSAA são ilustrados na seguinte imagem: ![ MSAA em DepthBasedComposition](./media/service-render-mode-balanced.png)

O antialiasing funciona corretamente entre a escultura e a cortina, porque ambas as partes são renderizadas na mesma GPU. Por outro lado, a borda entre a cortina e a parede mostra alguns pseudónimos porque estas duas partes são compostas por GPUs distintos.

A maior limitação deste modo é que as peças de geometria não podem ser mudadas para materiais transparentes dinamicamente, nem o modo **transparente** funciona para o [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Outras características de sobreposição de estado (contorno, tonalidade colorida, ...) funcionam, no entanto. Também os materiais que foram marcados como transparentes no tempo de conversão funcionam corretamente neste modo.

### <a name="performance"></a>Desempenho

As características de desempenho de ambos os modos variam em função do caso de utilização, e é difícil raciocinar ou fornecer recomendações gerais. Se não estiver limitado pelas limitações acima mencionadas (memória ou transparência/transparente), recomenda-se experimentar ambos os modos e monitorizar o desempenho utilizando várias posições da câmara.

## <a name="setting-the-render-mode"></a>Definição do modo de renderização

O modo de renderização utilizado num servidor de renderização remota é especificado durante `RenderingSession.ConnectAsync` a . `RendererInitOptions`

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# RenderingSession.ConnectAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions struct](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [RenderingSssion C++:ConnectToConnectAsyncRuntime()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions struct](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Passos seguintes

* [Sessões](../concepts/sessions.md)
* [Componente hierárquico de sobreposição de estado](../overview/features/override-hierarchical-state.md)