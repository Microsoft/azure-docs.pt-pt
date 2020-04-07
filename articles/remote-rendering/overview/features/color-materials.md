---
title: Materiais de cor
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681080"
---
# <a name="color-materials"></a>Materiais de cor

*Os materiais* de cor são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota azure. São usados para [malshes](../../concepts/meshes.md) que não devem receber qualquer tipo de iluminação, mas sim ser sempre com pleno brilho. Pode ser o caso de materiais 'brilhantes', como painéis de automóveis, lâmpadas ou dados que já incorporam iluminação estática, como modelos obtidos através da [fotogrammetria.](https://en.wikipedia.org/wiki/Photogrammetry)

Os materiais de cor são mais eficientes para renderizar do que [os materiais PBR](pbr-materials.md) devido ao seu modelo de sombreado mais simples. Também suportam diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades materiais comuns

Estas propriedades são comuns a todos os materiais:

* **albedoColor:** Esta cor é multiplicada com outras cores, tais como as cores *albedoMap* ou *vertex*. Se a *transparência* for ativada num material, o canal alfa `1` é utilizado para `0` ajustar a opacidade, com um significado totalmente opaco e com um significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente torna-se invisível. Isto é diferente para [materiais PBR.](pbr-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alfaClipEnabled** e **alphaClipThreshold:** Se *alphaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa albedo é inferior ao *alfaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa são ainda mais lentos para renderizar do que materiais totalmente opacos. Por padrão, o recorte alfa é desativado.

* **textureCoordinateScale** e **textureCoordinateOffset:** A balança é multiplicada nas coordenadas de textura UV, a compensação é adicionada a ela. Pode ser usado para esticar e deslocar as texturas. A escala predefinida é (1, 1) e a compensação é (0,0).

* **useVertexColor:** Se a malha contiver cores vértices e esta opção estiver ativada, as cores vértice das malhas são multiplicadas no *albedoColor* e *albedoMap*. Por padrão, as cores vértices são desativadas.

* **isDoubleSided:** Se a dupla faceta for verdadeira, os triângulos com este material são renderizados mesmo que a câmara esteja a olhar para as suas faces traseiras. Por defeito, esta opção está desativada. Ver também [renderização unilateral](single-sided-rendering.md).

## <a name="color-material-properties"></a>Propriedades de material de cor

As seguintes propriedades são específicas dos materiais de cor:

* **vertexMix:** Este valor `0` `1` entre e especifica quão fortemente a cor vértice numa [malha](../../concepts/meshes.md) contribui para a cor final. Pelo valor padrão de 1, a cor vértice é multiplicada na cor do albedo completamente. Com um valor de 0, as cores vértice são totalmente ignoradas.

* **transparênciaMode:** Ao contrário dos [materiais PBR,](pbr-materials.md)os materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo predefinido desativa a transparência. No entanto, o recorte alfa ainda é possível, e deve ser preferido, se for suficiente.
  
  1. **AlfaBlended:** Este modo é semelhante ao modo de transparência dos materiais PBR. Deve ser usado para materiais transparentes como vidro.

  1. **Aditivo:** Este modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Este modo pode ser utilizado para simular objetos brilhantes (mas ainda transparentes), como marcadores utilizados para realçar objetos importantes.

## <a name="next-steps"></a>Passos seguintes

* [Materiais PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Meshes](../../concepts/meshes.md)
