---
title: Materiais de cor
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: af33a777d2d6ef53965c2168ac0abee00f59bc50
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021386"
---
# <a name="color-materials"></a>Materiais de cor

*Os materiais* de cor são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota azure. São usados para [malshes](../../concepts/meshes.md) que não devem receber qualquer tipo de iluminação, mas sim ser sempre com pleno brilho. Pode ser o caso de materiais 'brilhantes', como painéis de automóveis, lâmpadas ou dados que já incorporam iluminação estática, como modelos obtidos através da [fotogrammetria.](https://en.wikipedia.org/wiki/Photogrammetry)

Os materiais de cor são mais eficientes para renderizar do que [os materiais PBR](pbr-materials.md) devido ao seu modelo de sombreado mais simples. Também suportam diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades materiais comuns

Estas propriedades são comuns a todos os materiais:

* **albedoColor:** Esta cor é multiplicada com outras cores, como o *albedoMap* ou * :::no-loc text="vertex"::: cores*. Se a *transparência* for ativada num material, o canal alfa é utilizado para ajustar a opacidade, com `1` um significado totalmente opaco e `0` com um significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente torna-se invisível. Isto é diferente para [materiais PBR.](pbr-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alfaClipEnabled** e **alphaClipThreshold:** Se *alphaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa albedo é inferior ao *alfaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir a transparência e é muito mais rápido de renderizar. No entanto, os materiais recortados alfa são ainda mais lentos para renderizar do que materiais totalmente opacos. Por padrão, o recorte alfa é desativado.

* **textureCoordinateScale** e **textureCoordinateOffset:** A balança é multiplicada nas coordenadas de textura UV, a compensação é adicionada a ela. Pode ser usado para esticar e deslocar as texturas. A escala predefinida é (1, 1) e a compensação é (0,0).

* **useVertexColor:** Se a malha contiver :::no-loc text="vertex"::: cores e esta opção estiver ativada, a cor das malhas :::no-loc text="vertex"::: é multiplicada no *albedoColor* e *albedoMap*. Por predefinição, *o VertexColor* está desativado.

* **isDoubleSided:** Se a dupla faceta for verdadeira, os triângulos com este material são renderizados mesmo que a câmara esteja a olhar para as suas faces traseiras. Por defeito, esta opção está desativada. Ver também [ :::no-loc text="Single-sided"::: renderização.](single-sided-rendering.md)

## <a name="color-material-properties"></a>Propriedades de material de cor

As seguintes propriedades são específicas dos materiais de cor:

* **vertexMix:** Este valor entre `0` e `1` especifica quão fortemente a :::no-loc text="vertex"::: cor numa [malha](../../concepts/meshes.md) contribui para a cor final. Pelo valor padrão de 1, a :::no-loc text="vertex"::: cor é multiplicada na cor do albedo completamente. Com um valor de 0, as :::no-loc text="vertex"::: cores são totalmente ignoradas.

* **transparênciaMode:** Ao contrário dos [materiais PBR,](pbr-materials.md)os materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo predefinido desativa a transparência. No entanto, o recorte alfa ainda é possível, e deve ser preferido, se for suficiente.
  
  1. **AlfaBlended:** Este modo é semelhante ao modo de transparência dos materiais PBR. Deve ser usado para materiais transparentes como vidro.

  1. **Aditivo:** Este modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Este modo pode ser utilizado para simular objetos brilhantes (mas ainda transparentes), como marcadores utilizados para realçar objetos importantes.

## <a name="next-steps"></a>Próximos passos

* [Materiais PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
