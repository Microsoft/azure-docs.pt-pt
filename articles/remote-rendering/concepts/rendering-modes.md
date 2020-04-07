---
title: Modos de renderização
description: Descreve os diferentes modos de renderização do lado do servidor
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681704"
---
# <a name="rendering-modes"></a>Modos de renderização

A renderização remota oferece dois modos principais de funcionamento, o modo **TileBasedComposition** e o modo **DepthBasedComposition.** Estes modos determinam como a carga de trabalho é distribuída por vários GPUs no servidor. O modo tem de ser especificado no momento da ligação e não pode ser alterado durante o tempo de funcionamento.

Ambos os modos vêm com vantagens, mas também com limitações inerentes à funcionalidade, por isso escolher o modo mais adequado é usar o caso específico.

## <a name="modes"></a>Modos

Os dois modos são agora discutidos mais detalhadamente.

### <a name="tilebasedcomposition-mode"></a>Modo 'TileBasedComposition'

No modo **TileBasedComposition,** todos os GPU envolvidos tornam subrectângulos específicos (azulejos) no ecrã. A GPU principal compõe a imagem final dos azulejos antes de ser enviada como uma moldura de vídeo para o cliente. Assim, todas as GPUs necessitam de ter o mesmo conjunto de recursos para renderização, pelo que os ativos carregados precisam de se encaixar na memória de uma única GPU.

A qualidade de renderização neste modo é ligeiramente melhor do que no modo **DepthBasedComposition,** uma vez que a MSAA pode trabalhar em todo o conjunto de geometria para cada GPU. A imagem que se segue mostra que o antialiasing funciona corretamente para ambas as bordas:

![MSAA em TileBasedComposition](./media/service-render-mode-quality.png)

Além disso, neste modo, cada peça pode ser mudada para um material transparente ou comutada para modo **transparente** através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Modo 'DepthBasedComposition'

No modo **DepthBasedComposition,** todos os GPU envolvidos renderizam em resolução completa do ecrã, mas apenas um subconjunto de meshes. A composição final da imagem na GPU principal tem o cuidado de as peças serem devidamente fundidas de acordo com as suas informações de profundidade. Naturalmente, a carga útil da memória é distribuída pelas GPUs, permitindo assim renderizar modelos que não se encaixam na memória de uma única GPU.

Cada GPU usa mSAA para antialias conteúdo local. No entanto, pode haver alias inerente entre bordas de GPUs distintos. Este efeito é atenuado pelo pós-processamento da imagem final, mas a qualidade da MSAA ainda é pior do que no modo **TileBasedComposition.**

Os artefactos da MSAA são ![ilustrados na seguinte imagem: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

O antialiasing funciona corretamente entre a escultura e a cortina, porque ambas as partes são renderizadas na mesma GPU. Por outro lado, a borda entre a cortina e a parede mostra algum pseudónimo porque estas duas partes são compostas por GPUs distintos.

A maior limitação deste modo é que as peças de geometria não podem ser trocadas para materiais transparentes dinamicamente, nem o modo **transparente** funciona para o [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Outras características de sobreposição do estado (contorno, cor, ...) funcionam. Também os materiais que foram marcados como transparentes no tempo de conversão funcionam corretamente neste modo.

### <a name="performance"></a>Desempenho

As características de desempenho de ambos os modos variam em função do caso de utilização, e é difícil raciocinar ou fornecer recomendações gerais. Se não estiver limitado pelas limitações acima mencionadas (memória ou transparência/transparente), recomenda-se experimentar ambos os modos e monitorizar o desempenho utilizando várias posições da câmara.

## <a name="setting-the-render-mode"></a>Definição do modo de renderização

O modo de renderização utilizado num VM `AzureSession.ConnectToRuntime` de `ConnectToRuntimeParams`renderização remota é especificado durante o . .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Passos seguintes

* [Sessões](../concepts/sessions.md)
* [Componente hierárquico de sobreposição do estado](../overview/features/override-hierarchical-state.md)
