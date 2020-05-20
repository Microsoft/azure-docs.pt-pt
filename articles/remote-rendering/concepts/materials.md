---
title: Materiais
description: Descrição do material de renderização e propriedades materiais
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 1a45dc39f5a0795684aba278391e04f6af3f7b55
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659679"
---
# <a name="materials"></a>Materiais

Os [materiais](../concepts/lifetime.md) são recursos partilhados que definem como as [meshes](meshes.md) são renderizadas. Os materiais são usados para especificar quais texturas a aplicar, se tornar os [objetos](textures.md) transparentes e como a iluminação será calculada.

Os materiais são automaticamente criados durante a conversão do [modelo](../how-tos/conversion/model-conversion.md) e são acessíveis em tempo de execução. Também pode criar materiais personalizados a partir de código e substituir os existentes. Este cenário faz especial mente se quiser partilhar o mesmo material em muitas malsheas. Uma vez que as modificações de um material são visíveis em todas as malhas que o referenciam, este método pode ser usado para aplicar facilmente alterações.

> [!NOTE]
> Alguns casos de utilização, como destacar um objeto escolhido, podem ser feitos modificando materiais, mas são muito mais fáceis de conseguir através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipos de materiais

A renderização remota Azure tem dois tipos de material distintos:

* Os [materiais PBR](../overview/features/pbr-materials.md) são utilizados para superfícies que devem ser tornadas o mais fisicamente corretas possível. A iluminação realista é calculada para estes materiais utilizando *renderização baseada fisicamente* (PBR). Para tirar o máximo partido deste tipo de material, é importante fornecer dados de entrada de alta qualidade, como asperezas e mapas normais.

* São utilizados [materiais](../overview/features/color-materials.md) de cor para casos em que não é desejada iluminação adicional. Estes materiais são sempre brilhantes e são mais fáceis de configurar. Os materiais de cor são utilizados para dados que não devem ter iluminação, ou já incorpora iluminação estática, como modelos obtidos através da [fotogrammetria.](https://en.wikipedia.org/wiki/Photogrammetry)

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Atribuição de material de malha vs. MalhaComponent

[As malsheas](meshes.md) têm uma ou mais submeshes. Cada submalha refere um material. Pode alterar o material para utilizar diretamente na malha, ou pode substituir o material a utilizar para uma submalha num Componente de [Malha](meshes.md#meshcomponent).

Quando modifica um material diretamente no recurso de malha, esta alteração afeta todos os casos dessa malha. No entanto, a alteração do componente de malha só afeta essa instância de malha. Qual o método mais apropriado depende do comportamento desejado, mas modificar um MeshComponent é a abordagem mais comum.

## <a name="material-de-duplication"></a>Desduplicação de material

Durante a conversão, vários materiais com as mesmas propriedades e texturas são automaticamente desduplicados num único material. Pode desativar esta funcionalidade nas definições de [conversão,](../how-tos/conversion/configure-model-conversion.md)mas recomendamos deixá-la ativa da melhor forma.

## <a name="material-classes"></a>Aulas de material

Todos os materiais fornecidos pela API derivam da classe `Material` base. O seu tipo pode ser consultado através `Material.MaterialSubType` ou lançando-os diretamente:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Materiais PBR](../overview/features/pbr-materials.md)
* [Materiais de cor](../overview/features/color-materials.md)
