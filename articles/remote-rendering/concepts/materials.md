---
title: Materiais
description: Descrição do material de renderização e propriedades materiais
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4406e3875bd2d56a33bcc9a94a24ad874fecf8da
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592182"
---
# <a name="materials"></a>Materiais

Os materiais são [recursos partilhados](../concepts/lifetime.md) que definem como [as malhas](meshes.md) são renderizadas. Os materiais são utilizados para especificar quais [as texturas](textures.md) a aplicar, se para tornar os objetos transparentes e como a iluminação será calculada.

Os materiais são criados automaticamente durante [a conversão do modelo](../how-tos/conversion/model-conversion.md) e são acessíveis em tempo de execução. Também pode criar materiais personalizados a partir do código e substituir os existentes. Este cenário faz especialmente sentido se quiser partilhar o mesmo material através de muitas malhas. Uma vez que as modificações de um material são visíveis em cada malha que o referencia, este método pode ser usado para aplicar facilmente alterações.

> [!NOTE]
> Alguns casos de uso, como destacar um objeto escolhido pode ser feito modificando materiais, mas são muito mais fáceis de alcançar através do [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipos de materiais

A renderização remota Azure tem dois tipos distintos de material:

* [Os materiais PBR](../overview/features/pbr-materials.md) são utilizados para superfícies que devem ser o mais corretas fisicamente possível. A iluminação realista é calculada para estes materiais utilizando *renderização física* (PBR). Para tirar o máximo partido deste tipo de material, é importante fornecer dados de entrada de alta qualidade, como aspereza e mapas normais.

* [Os materiais de](../overview/features/color-materials.md) cor são utilizados em casos em que não é desejada iluminação adicional. Estes materiais são sempre cheios de brilho e são mais fáceis de configurar. Os materiais de cor são utilizados para dados que não devem ter iluminação, ou que já incorporam iluminação estática, como modelos obtidos através de [fotogrammetria.](https://en.wikipedia.org/wiki/Photogrammetry)

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Malha vs. MeshComponent atribuição de material

[As malhas](meshes.md) têm uma ou mais submechas. Cada submesh refere um material. Pode alterar o material para utilizar diretamente na malha, ou pode substituir o material a utilizar para um submesh numa [MeshComponent](meshes.md#meshcomponent).

Quando modifica um material diretamente no recurso de malha, esta alteração afeta todas as instâncias dessa malha. A sua alteração no MeshComponent, no entanto, apenas afeta essa única instância de malha. Qual método mais apropriado depende do comportamento desejado, mas modificar um MeshComponent é a abordagem mais comum.

## <a name="material-de-duplication"></a>Des duplicação de material

Durante a conversão, vários materiais com as mesmas propriedades e texturas são automaticamente desprovaduídos num único material. Pode desativar esta função nas definições de [conversão,](../how-tos/conversion/configure-model-conversion.md)mas recomendamos deixá-la on para melhor desempenho.

## <a name="material-classes"></a>Aulas de material

Todos os materiais fornecidos pela API derivam da classe `Material` base. O seu tipo pode ser questionado através `Material.MaterialSubType` ou lançando-os diretamente:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```

## <a name="api-documentation"></a>Documentação da API

* [Classe de material](/dotnet/api/microsoft.azure.remoterendering.material)
* [C# Aula colormaterial](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [Classe PbrMaterial](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection.CreateMaterial()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [Classe de material C++](/cpp/api/remote-rendering/material)
* [Classe CorMaterial C++](/cpp/api/remote-rendering/colormaterial)
* [Classe PbrMaterial C++](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection::CreateMaterial()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Passos seguintes

* [Materiais PBR](../overview/features/pbr-materials.md)
* [Materiais de cor](../overview/features/color-materials.md)