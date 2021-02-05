---
title: Efeito de Fresnel
description: Página de explicação de recurso para o efeito material do fresnel
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594117"
---
# <a name="fresnel-effect"></a>Efeito de Fresnel

A característica do material do efeito fresnel é um efeito ad-hoc não fisicamente correto. A funcionalidade baseia-se na observação física dos objetos que se tornam mais reflexivos nestes ângulos. A própria reflexão fresnel já está fisicamente incorporada no [modelo de material PBR](../../overview/features/pbr-materials.md) utilizado na renderização remota Azure. Em contraste, a característica do material do efeito fresnel é apenas um efeito de cor aditivo sem dependência das [luzes](../../overview/features/lights.md) ou do ambiente do [céu.](../../overview/features/sky.md)

O efeito fresnel dá aos objetos afetados um brilho colorido em torno das suas bordas. As informações sobre a personalização de efeitos e exemplos dos resultados de renderização podem ser encontradas nas seguintes secções.

## <a name="enabling-the-fresnel-effect"></a>Permitindo o efeito fresnel

Para utilizar a função efeito fresnel, tem de ser ativada sobre os materiais em questão. Pode ative-lo definindo a parte FresnelEffect das [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) no [material PBR](../../overview/features/pbr-materials.md). O mesmo padrão aplica-se às [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) e ao [material Color.](../../overview/features/color-materials.md) Consulte a secção de amostras de código para obter uma demonstração de utilização.

Após a ativação, o efeito fresnel será imediatamente visível. Por defeito, o brilho será branco (1, 1, 1, 1) e terá um expoente de 1. Pode personalizar estas definições utilizando os parâmetros abaixo.

## <a name="customizing-the-effect-appearance"></a>Personalizar a aparência do efeito

Atualmente, o efeito fresnel pode ser personalizado por material usando as seguintes propriedades:

| Propriedade material | Tipo | Explicação |
|-------------------|------|-------------|
| FresnelEffectColor | Cor4 | A cor que é adicionada no máximo à medida que o fresnel brilha. O canal alfa é atualmente ignorado. |
| FresnelEffectExponent | float | A propagação do brilho do fresnel. Varia de 0,01 (espalhados por todo o objeto) a 10 (apenas os ângulos mais brilhantes). |

Na prática, diferentes cores e configurações de expoente serão assim:

![Exemplos de efeito Fresnel](./media/fresnel-effect-examples.png)

O expoente do efeito fresnel é progressivamente aumentado de 1 para 10 para cada linha de cor. Ao fazê-lo, puxa progressivamente o brilho do fresnel para as bordas dos objetos vistos. O efeito fresnel também não é afetado pela transparência, como pode ver no seguinte exemplo:

![Exemplos de transparência do efeito Fresnel](./media/fresnel-effect-transparent-examples.png)

Como mostrado, os objetos na diagonal são totalmente transparentes, mas o brilho do fresnel permanece. O efeito imita o fresnel fisicamente baseado a este respeito, que também está presente nestas imagens.

## <a name="code-samples"></a>Exemplos de código

As seguintes amostras de código mostram que permite e personaliza o efeito fresnel tanto para um [material PBR](../../overview/features/pbr-materials.md) como para um [material de cor:](../../overview/features/color-materials.md)

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>Documentação da API

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Passos seguintes

* [Materiais](../../concepts/materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)
* [Materiais de cor](../../overview/features/color-materials.md)