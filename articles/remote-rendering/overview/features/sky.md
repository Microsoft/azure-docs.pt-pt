---
title: Reflexos do céu
description: Descreve como configurar mapas ambientais para reflexões no céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613761"
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
| Não-Metal  | ![Dielectric, Aspereza=0](media/dielectric-0.png)   | ![Dielectric, Aspereza=0,25](media/dielectric-0.25.png)  | ![Dielectric, Aspereza=0.5](media/dielectric-0.5.png)  | ![Dielectric, Aspereza=0,75](media/dielectric-0.75.png)  | ![Dielectric, Aspereza=1](media/dielectric-1.png)  |
| Metal      | ![Metal, Aspereza=0](media/metallic-0.png)  | ![Metal, Aspereza=0,25](media/metallic-0.25.png)    | ![Metal, Aspereza=0.5](media/metallic-0.5.png)    | ![Metal, Aspereza=0,75](media/metallic-0.75.png)    | ![Metal, Aspereza=1](media/metallic-1.png)    |

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
                printf("Texture loading failed!\n");
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
|builtin://Autoshop                 | Variedade de luzes de listras, iluminação de base interior brilhante    | ![Autoshop skybox usado para iluminar um objeto](media/autoshop.png)
|builtin://BoilerRoom               | Definição de luz interior brilhante, múltiplas luzes de janela      | ![Cabine de água da Caldeira usada para iluminar um objeto](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes coloridas variadamente na configuração interior de luz média  | ![Skybox colorida dotudio usado para iluminar um objeto](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante                     | ![A skybox SmallHangar usada para iluminar um objeto](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Definição interior fraca com contraste claro-escuro              | ![IndustrialPipeAndValve skybox usado para iluminar um objeto](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luz ambiente diurna, luz de área de janela brilhante     | ![Skybox Lebombo usado para acender um objeto](media/lebombo.png)
|builtin://SataraNight              | Céu noturno escuro e chão com muitas luzes circundantes   | ![SataraNight skybox usado para iluminar um objeto](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz solar brilhante e contraste de sombra                      | ![SunnyVondelpark skybox usado para iluminar um objeto](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luz clara do céu com iluminação terrestre moderada            | ![Skybox Syferfontein usado para iluminar um objeto](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e sombra moderadamente variáveis                         | ![TearsOfSteelBridge skybox usado para acender um objeto](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luz do pôr do sol da noite aproximando-se do crepúsculo                    | ![A skybox VeniceSunset usada para acender um objeto](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tons de luz brilhantes, verde-exuberantes e brancos, terreno escurecido | ![WhippleCreekRegionalPark skybox usado para iluminar um objeto](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dia diurno com luz de terra ambiente brilhante                 | ![Skybox WinterRiver usado para iluminar um objeto](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![Skybox DefaultSky usado para iluminar um objeto](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Documentação da API

* [C# RemoteManager.SkyReflectionSettings propriedade](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ RemoteManager::SkyReflectionSettings()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Passos seguintes

* [Lights](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta de linha de comando TexConv](../../resources/tools/tex-conv.md)
