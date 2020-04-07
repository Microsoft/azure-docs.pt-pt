---
title: Reflexos do céu
description: Descreve como configurar mapas ambientais para reflexos do céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680612"
---
# <a name="sky-reflections"></a>Reflexos do céu

Na Renderização Remota Azure, uma textura do céu é usada para iluminar objetos de forma realista. Para aplicações de realidade aumentada, esta textura deve assemelhar-se ao seu ambiente real, para fazer com que os objetos pareçam convincentes. Este artigo descreve como mudar a textura do céu.

> [!NOTE]
> A textura do céu também é referida como um *mapa ambiental.* Estes termos são usados intercambiavelmente.

## <a name="object-lighting"></a>Iluminação de objetos

A Azure Remote Rendering emprega *renderização baseada fisicamente* (PBR) para computações de iluminação realistas. Embora possa adicionar [fontes de luz](lights.md) à sua cena, usar uma boa textura do céu tem o maior impacto.

As imagens abaixo mostram resultados de iluminação de diferentes superfícies apenas com uma textura do céu:

| Aspereza  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Não-Metal  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Para obter mais informações sobre o modelo de iluminação, consulte o capítulo dos [materiais.](../../concepts/materials.md)

> [!IMPORTANT]
> A Renderização Remota Azure utiliza a textura do céu apenas para modelos de iluminação. Não torna o céu como pano de fundo, uma vez que as aplicações de Realidade Aumentada já têm um passado adequado - o mundo real.

## <a name="changing-the-sky-texture"></a>Mudando a textura do céu

Para alterar o mapa ambiental, tudo o que precisa fazer `SkyReflectionSettings`é carregar uma [textura](../../concepts/textures.md) e mudar a sessão:

``` cs
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

Note que `LoadTextureFromSASAsync` a variante é usada acima porque uma textura incorporada é carregada. Em caso de carregamento a partir de `LoadTextureAsync` [armazenamentos de blob ligados,](../../how-tos/create-an-account.md#link-storage-accounts)utilize a variante.

## <a name="sky-texture-types"></a>Tipos de textura do céu

Você pode usar ambos *[os cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* e *texturas 2D* como mapas de ambiente.

Todas as texturas têm de estar num formato de [textura suportada.](../../concepts/textures.md#supported-texture-formats) Não é preciso fornecer mipmaps para texturas do céu.

### <a name="cube-environment-maps"></a>Mapas do ambiente do cubo

Para referência, aqui está um mapa cubo desembrulhado:

![Um mapa cubo desembrulhado](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Use `LoadTextureFromSASAsync` `TextureType.CubeMap` com para carregar texturas de cubos.

### <a name="sphere-environment-maps"></a>Mapas do ambiente da esfera

Quando se usa uma textura 2D como mapa ambiental, a imagem tem de estar no espaço de [coordenadas esféricas.](https://en.wikipedia.org/wiki/Spherical_coordinate_system)

![Uma imagem do céu em coordenadas esféricas](media/spheremap-example.png)

Utilize `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` com para carregar mapas ambientais esféricos.

## <a name="built-in-environment-maps"></a>Mapas ambientais embutidos

A Azure Remote Rendering fornece alguns mapas ambientais incorporados que estão sempre disponíveis. Todos os mapas ambientais embutidos são mapas de cubos.

|Identificador                         | Descrição                                              | Ilustração                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Variedade de luzes de listras, iluminação interior brilhante da base    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Ajuste de luz interior brilhante, múltiplas luzes da janela      | ![Sala da Caldeira](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes variadamente coloridas em ambiente interior de luz média  | ![Estúdio Colorido](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante                     | ![Pequeno Hangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Ajuste interior escuro com contraste escuro-claro              | ![Tubagem industrial e válvula](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luz ambiente diurna da sala, luz de área de janela brilhante     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Céu noturno escuro e chão com muitas luzes circundantes   | ![Noite de Satara](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz solar brilhante e contraste de sombra                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Clara luz do céu com iluminação terrestre moderada            | ![Syferfonteina](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e sombra moderadamente variáveis                         | ![Ponte TearsofSteel](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luz do pôr do sol da noite aproximando-se do crepúsculo                    | ![VenezaSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tons brilhantes, verde-exuberante, e luz branca, chão escurecido | ![Parque Regional WhippleCreek](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Durante o dia com luz de chão ambiente brilhante                 | ![Rio Winter](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Passos seguintes

* [Lights](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta de linha de comando TexConv](../../resources/tools/tex-conv.md)
