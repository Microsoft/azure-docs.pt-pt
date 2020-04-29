---
title: Luzes
description: Descrição e propriedades de fonte de luz
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680950"
---
# <a name="lights"></a>Luzes

Por defeito, os objetos telecomandados são iluminados com uma [luz do céu](sky.md). Para a maioria das aplicações isso já é suficiente, mas pode adicionar mais fontes de luz ao local.

> [!IMPORTANT]
> Apenas [os materiais PBR](pbr-materials.md) são afetados por fontes luminosas. [Os materiais](color-materials.md) de cor parecem sempre totalmente brilhantes.

> [!NOTE]
> As sombras de casting não são atualmente suportadas. A Renderização Remota Azure é otimizada para renderizar enormes quantidades de geometria, utilizando múltiplas GPUs, se necessário. As abordagens tradicionais para o casting de sombras não funcionam bem nesses cenários.

## <a name="common-light-component-properties"></a>Propriedades comuns dos componentes da luz

Todos os tipos de luz `LightComponent` derivam da classe base abstrata e partilham estas propriedades:

* **Cor:** A cor da luz no [espaço Gama.](https://en.wikipedia.org/wiki/SRGB) Alpha é ignorado.

* **Intensidade:** O brilho da luz. Para luzes de ponto e ponto, a intensidade também define até onde a luz brilha.

## <a name="point-light"></a>Luz de ponto

Na Renderização remota `PointLightComponent` azure, a luz pode não só emitir luz de um único ponto, mas também de uma pequena esfera ou de um pequeno tubo, para simular fontes de luz mais suaves.

### <a name="pointlightcomponent-properties"></a>Propriedades de Componentes PointLight

* **Raio:** O raio padrão é zero, caso em que a luz funciona como uma luz de ponta. Se o raio for maior que zero, atua como fonte de luz esférica, o que altera o aparecimento de destaques especular.

* **Comprimento:** Se `Length` ambos `Radius` e não forem zero, a luz funciona como uma luz de tubo. Isto pode ser usado para simular tubos de néon.

* **AtenuaçãoCutoff:** Se deixada para (0,0) a atenuação da luz `Intensity`depende apenas da sua . No entanto, pode fornecer distâncias min/max personalizadas sobre as quais a intensidade da luz é reduzida a 0. Esta funcionalidade pode ser usada para impor uma menor gama de influências de uma luz específica.

* **ProjectedCubemap:** Se definido para um [mapa de cubos](../../concepts/textures.md)válido, a textura é projetada sobre a geometria circundante da luz. A cor do mapa do cubo é modulada com a cor da luz.

## <a name="spot-light"></a>Luz de mancha

A `SpotLightComponent` é semelhante `PointLightComponent` à, mas a luz está limitada à forma de um cone. A orientação do cone é definida pelo *eixo z negativo da entidade proprietária.*

### <a name="spotlightcomponent-properties"></a>Propriedades de Componentes SpotLight

* **Raio:** O mesmo `PointLightComponent`que para o.

* **SpotAngleDeg:** Este intervalo define o ângulo interno e externo do cone, medido em grau. Tudo dentro do ângulo interno é iluminado com pleno brilho. Uma queda é aplicada para o ângulo exterior que gera um efeito semelhante a penumbra.

* **FalloffExponent:** Define o quão acentuadamente as transições de falloff entre o ângulo interno e o cone exterior. Um valor mais elevado resulta numa transição mais nítida. O incumprimento de 1.0 resulta numa transição linear.

* **AtenuaçãoCutoff:** O mesmo `PointLightComponent`que para o.

* **Textura projetada2d:** Se definida para uma [textura 2D](../../concepts/textures.md)válida, a imagem é projetada na geometria a que a luz brilha. A cor da textura é modulada com a cor da luz.

## <a name="directional-light"></a>Luz direcional

O `DirectionalLightComponent` simula uma fonte de luz que está infinitamente longe. A luz brilha na direção do *eixo z negativo da entidade proprietária.* A posição da entidade é ignorada.

Não há propriedades adicionais.

## <a name="performance-considerations"></a>Considerações de desempenho

As fontes de luz têm um impacto significativo no desempenho da renderização. Utilize-os cuidadosamente e apenas se necessário pela aplicação. Qualquer condição de iluminação global estática, incluindo um componente direcional estático, pode ser alcançada com uma [textura de céu personalizada,](sky.md)sem custo adicional de renderização.

## <a name="next-steps"></a>Passos seguintes

* [Materiais](../../concepts/materials.md)
* [Céu](sky.md)
