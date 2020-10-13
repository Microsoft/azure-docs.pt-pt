---
title: Materiais de cor
description: Descreve o tipo de material de cor.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 93fde2d20aaa5b7bac4adc6f1d7fb076569e4bb8
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893565"
---
# <a name="color-materials"></a>Materiais de cor

*Os materiais de* cor são um dos tipos de [material](../../concepts/materials.md) suportado na renderização remota Azure. São utilizados para [malhas](../../concepts/meshes.md) que não devem receber qualquer tipo de iluminação, mas sim estar sempre com brilho total. Este pode ser o caso de materiais "brilhantes", como painéis de automóveis, lâmpadas ou dados que já incorporam iluminação estática, como modelos obtidos através de [fotogrammetria.](https://en.wikipedia.org/wiki/Photogrammetry)

Os materiais de cor são mais eficientes para renderizar do que [os materiais PBR](pbr-materials.md) devido ao seu modelo de sombreamento mais simples. Apoiam igualmente diferentes modos de transparência.

## <a name="common-material-properties"></a>Propriedades materiais comuns

Estas propriedades são comuns a todos os materiais:

* **albedoColor:** Esta cor é multiplicada com outras cores, como o *albedoMap* ou * :::no-loc text="vertex"::: as cores.* Se *a transparência* for ativada num material, o canal alfa é utilizado para ajustar a opacidade, com significado totalmente `1` opaco e `0` significado totalmente transparente. O padrão é branco.

  > [!NOTE]
  > Como os materiais de cor não refletem o ambiente, um material de cor totalmente transparente torna-se invisível. Isto é diferente para [materiais PBR.](pbr-materials.md)

* **albedoMap:** Uma [textura 2D](../../concepts/textures.md) para valores de albedo por pixel.

* **alphaClipEnabled** e **alphaClipThreshold:** Se *alfaClipEnabled* for verdadeiro, todos os pixels onde o valor alfa albedo é inferior ao *alphaClipThreshold* não serão desenhados. O recorte alfa pode ser usado mesmo sem permitir a transparência e é muito mais rápido de renderizar. No entanto, os materiais alfa cortados são ainda mais lentos a renderizar do que materiais totalmente opacos. Por defeito, o recorte alfa é desativado.

* **textureCoordinateScale** e **textureCoordinateOffset:** A escala é multiplicada nas coordenadas de textura UV, o offset é adicionado a ele. Pode ser usado para esticar e deslocar as texturas. A escala predefinida é (1, 1) e a offset é (0,0).

* **useVertexColor:** Se a malha contiver :::no-loc text="vertex"::: cores e esta opção estiver ativada, a cor das malhas :::no-loc text="vertex"::: é multiplicada no *albedoColor* e *albedoMap.* Por *utilização predefinidaVertexColor* é desativado.

* **isDoubleSided:** Se a dupla faceta for verdadeira, os triângulos com este material são renderizados mesmo que a câmara esteja a olhar para as suas faces traseiras. Por predefinição, esta opção é desativada. Ver também [ :::no-loc text="Single-sided"::: renderização.](single-sided-rendering.md)

* **TransparênciaDesseepth:** Se a bandeira TransparencyWritesDepth for colocada no material e o material for transparente, os objetos que utilizam este material também contribuirão para o tampão de profundidade final. Consulte a transparência da propriedade do material *de corMode* na secção seguinte. Para ativar esta funcionalidade é recomendado se o seu caso de utilização necessitar de uma [reprojecção tardia](late-stage-reprojection.md) mais plausível de cenas totalmente transparentes. Para cenas opacas/transparentes mistas, este cenário pode introduzir comportamentos de reprojecção implausíveis ou artefactos de reprojecção. Por esta razão, a definição predefinida e recomendada para o caso de utilização geral é para desativar esta bandeira. Os valores de profundidade escrita são retirados da camada de profundidade por pixel do objeto mais próximo da câmara.

## <a name="color-material-properties"></a>Propriedades de materiais de cor

As seguintes propriedades são específicas dos materiais de cor:

* **vertexMix:** Este valor entre `0` e `1` especifica quão fortemente a :::no-loc text="vertex"::: cor numa [malha](../../concepts/meshes.md) contribui para a cor final. Pelo valor padrão de 1, a :::no-loc text="vertex"::: cor é multiplicada na cor albedo completamente. Com um valor de 0, as :::no-loc text="vertex"::: cores são ignoradas inteiramente.

* **transparênciaMode:** Ao contrário dos [materiais PBR,](pbr-materials.md)os materiais de cor distinguem entre diferentes modos de transparência:

  1. **Opaco:** O modo predefinido desativa a transparência. O recorte alfa ainda é possível, no entanto, e deve ser preferido, se for suficiente.
  
  1. **AlfaBlended:** Este modo é semelhante ao modo de transparência para materiais PBR. Deve ser utilizado para materiais transparentes como vidro.

  1. **Aditivo:** Este modo é o modo de transparência mais simples e eficiente. A contribuição do material é adicionada à imagem renderizada. Este modo pode ser utilizado para simular objetos brilhantes (mas ainda transparentes), como marcadores utilizados para realçar objetos importantes.

## <a name="api-documentation"></a>Documentação da API

* [C# Aula colormaterial](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C# RemoteManager.CreateMaterial()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [Classe CorMaterial C++](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterial)
* [C++ RemoteManager::CreateMaterial()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>Passos seguintes

* [Materiais PBR](pbr-materials.md)
* [Texturas](../../concepts/textures.md)
* [Malhas](../../concepts/meshes.md)
