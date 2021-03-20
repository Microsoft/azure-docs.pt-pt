---
title: Iluminação da cena
description: Descrição e propriedades de fonte de luz
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206854"
---
# <a name="scene-lighting"></a>Iluminação da cena

Por predefinição, os objetos renderizados remotamente são acesos utilizando uma [luz do céu](sky.md). Para a maioria das aplicações isso já é suficiente, mas pode adicionar mais fontes de luz ao local.

> [!IMPORTANT]
> Apenas [os materiais PBR](pbr-materials.md) são afetados por fontes luminosas. [Os materiais de cor](color-materials.md) sempre aparecem totalmente brilhantes.

> [!NOTE]
> As sombras de casting não são suportadas atualmente. A renderização remota Azure é otimizada para renderizar enormes quantidades de geometria, utilizando várias GPUs se necessário. As abordagens tradicionais para o casting de sombras não funcionam bem nesses cenários.

## <a name="common-light-component-properties"></a>Propriedades comuns de componentes de luz

Todos os tipos de luz derivam da classe base abstrata `LightComponent` e partilham estas propriedades:

* **Cor:** A cor da luz no [espaço Gama.](https://en.wikipedia.org/wiki/SRGB) Alpha é ignorado.

* **Intensidade:** O brilho da luz. Para luzes de ponto e ponto, a intensidade também define até onde a luz brilha.

## <a name="point-light"></a>Luz de ponto

Na Renderização Remota Azure, `PointLightComponent` a pode não só emitir luz de um único ponto, mas também de uma pequena esfera ou de um pequeno tubo, para simular fontes de luz mais suaves.

### <a name="pointlightcomponent-properties"></a>Propriedades PointLightComponent

* **Raio:** O raio padrão é zero, caso em que a luz atua como uma luz de ponto. Se o raio for maior que zero, atua como fonte de luz esférica, o que altera o aparecimento de destaques espásicos.

* **Comprimento:** Se ambos `Length` e `Radius` não forem nulos, a luz atua como uma luz de tubo. Isto pode ser usado para simular tubos de néon.

* **AtenuaçãoCutoff:** Se for deixada a (0,0) a atenuação da luz só depende da sua `Intensity` . No entanto, pode fornecer distâncias min/max personalizadas sobre as quais a intensidade da luz é reduzida linearmente para 0. Esta funcionalidade pode ser usada para impor uma menor gama de influência de uma luz específica.

* **Mapa projetado doCubemap:** Se for definido para um [mapa cúbico](../../concepts/textures.md)válido, a textura é projetada sobre a geometria circundante da luz. A cor do cubo é modulada com a cor da luz.

## <a name="spot-light"></a>Luz de spot

O `SpotLightComponent` é semelhante ao mas a luz está limitada à forma de um `PointLightComponent` cone. A orientação do cone é definida pelo *eixo z negativo da entidade proprietária.*

### <a name="spotlightcomponent-properties"></a>Propriedades spotLightComponent

* **Raio:** O mesmo que para `PointLightComponent` o.

* **SpotAngleDeg:** Este intervalo define o ângulo interno e externo do cone, medido em grau. Tudo dentro do ângulo interno é iluminado com pleno brilho. Uma queda é aplicada para o ângulo exterior que gera um efeito semelhante à penumbra.

* **FalloffExponent:** Define como a queda transite acentuadamente entre o ângulo interno e o cone exterior. Um valor mais elevado resulta numa transição mais acentuada. O padrão de 1.0 resulta numa transição linear.

* **AtenuaçãoCutoff:** O mesmo que para `PointLightComponent` o.

* **Projeção2dTextura:** Se for definida com uma [textura 2D](../../concepts/textures.md)válida, a imagem é projetada para a geometria em que a luz brilha. A cor da textura é modulada com a cor da luz.

## <a name="directional-light"></a>Luz direcional

A `DirectionalLightComponent` simula uma fonte de luz que está infinitamente longe. A luz brilha na direção do *eixo z negativo da entidade proprietária.* A posição da entidade é ignorada.

Não há propriedades adicionais.

## <a name="performance-considerations"></a>Considerações de desempenho

As fontes luminosas têm um impacto significativo no desempenho da renderização. Utilize-os cuidadosamente e apenas se for necessário pela aplicação. Qualquer condição de iluminação global estática, incluindo um componente direcional estático, pode ser alcançada com uma [textura de céu personalizada,](sky.md)sem custo adicional de renderização.

## <a name="api-documentation"></a>Documentação da API

* [C# Classe LightComponentBase](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# Classe PointLightComponent](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [Classe C++ LightComponentBase](/cpp/api/remote-rendering/lightcomponentbase)
* [Classe C++ PointLightComponent](/cpp/api/remote-rendering/pointlightcomponent)
* [Classe C++ SpotLightComponent](/cpp/api/remote-rendering/spotlightcomponent)
* [Classe C++ DirectionalLightComponent](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Passos seguintes

* [Materiais](../../concepts/materials.md)
* [Céu](sky.md)