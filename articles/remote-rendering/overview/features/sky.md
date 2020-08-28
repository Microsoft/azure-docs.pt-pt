---
title: Reflexos do céu
description: Descreve como configurar mapas ambientais para reflexões no céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f2a871e409761116182f67eb877f3727038fe0dc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013644"
---
# <a name="sky-reflections"></a>Reflexos do céu

Na renderização remota de Azure, uma textura do céu é usada para iluminar objetos de forma realista. Para aplicações de realidade aumentada, esta textura deve assemelhar-se ao seu ambiente real, para fazer com que os objetos pareçam convincentes. Este artigo descreve como mudar a textura do céu.

> [!NOTE]
> A textura do céu também é referida como um *mapa ambiental.* Estes termos são usados intercambiavelmente.

## <a name="object-lighting"></a>Iluminação de objetos

A renderização remota Azure emprega *renderização física* (PBR) para computaçãos de iluminação realistas. Embora possa adicionar [fontes de luz](lights.md) à sua cena, usar uma boa textura do céu tem o maior impacto.

As imagens abaixo mostram resultados de iluminação de diferentes superfícies apenas com uma textura de céu:

| Aspereza  | 0                                        | 0.25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Não-Metal  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Para obter mais informações sobre o modelo de iluminação, consulte o capítulo dos [materiais.](../../concepts/materials.md)

> [!IMPORTANT]
> A renderização remota Azure usa a textura do céu apenas para modelos de iluminação. Não torna o céu como pano de fundo, uma vez que as aplicações de Realidade Aumentada já têm um fundo próprio - o mundo real.

## <a name="changing-the-sky-texture"></a>Mudando a textura do céu

Para alterar o mapa ambiental, tudo o que precisa fazer é [carregar uma textura](../../concepts/textures.md) e alterar a sessão: `SkyReflectionSettings`

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!");
            }
        });
}

```

Note que a `LoadTextureFromSASAsync` variante é usada acima porque uma textura incorporada é carregada. Em caso de carregamento a partir de [armazenamentos de bolhas ligados,](../../how-tos/create-an-account.md#link-storage-accounts)utilize a `LoadTextureAsync` variante.

## <a name="sky-texture-types"></a>Tipos de textura do céu

Você pode usar tanto *[os cubos](https://en.wikipedia.org/wiki/Cube_mapping)* como *as texturas 2D como mapas ambientais.*

Todas as texturas têm de estar num [formato de textura suportado.](../../concepts/textures.md#supported-texture-formats) Você não precisa fornecer mipmaps para texturas do céu.

### <a name="cube-environment-maps"></a>Mapas do ambiente do cubo

Para referência, aqui está um mapa de cubos desembrulhado:

![Um mapa de cubos desembrulhado](media/Cubemap-example.png)

Use `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` com para `TextureType.CubeMap` carregar texturas de mapas de cubos.

### <a name="sphere-environment-maps"></a>Mapas do ambiente da esfera

Ao utilizar uma textura 2D como mapa ambiental, a imagem tem de estar no [espaço de coordenadas esféricas.](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

![Uma imagem do céu em coordenadas esféricas](media/spheremap-example.png)

Use `AzureSession.Actions.LoadTextureAsync` com para carregar `TextureType.Texture2D` mapas de ambiente esférico.

## <a name="built-in-environment-maps"></a>Mapas ambientais incorporados

A renderização remota Azure fornece alguns mapas ambientais incorporados que estão sempre disponíveis. Todos os mapas ambientais incorporados são cubos.

|Identificador                         | Descrição                                              | Ilustração                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Variedade de luzes de listras, iluminação de base interior brilhante    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Definição de luz interior brilhante, múltiplas luzes de janela      | ![Sala da Caldeira](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes coloridas variadamente na configuração interior de luz média  | ![Studio colorido](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante                     | ![Pequeno Hangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Definição interior fraca com contraste claro-escuro              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luz ambiente diurna, luz de área de janela brilhante     | ![Rio Lebombo](media/lebombo.png)
|builtin://SataraNight              | Céu noturno escuro e chão com muitas luzes circundantes   | ![Noite de Satara](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz solar brilhante e contraste de sombra                      | ![Parque SunnyVondel](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luz clara do céu com iluminação terrestre moderada            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e sombra moderadamente variáveis                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luz do pôr do sol da noite aproximando-se do crepúsculo                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tons de luz brilhantes, verde-exuberantes e brancos, terreno escurecido | ![Parque WhippleCreekRegional](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dia diurno com luz de terra ambiente brilhante                 | ![Rio WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![PadrãoSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Passos seguintes

* [Luzes](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta de linha de comando TexConv](../../resources/tools/tex-conv.md)
