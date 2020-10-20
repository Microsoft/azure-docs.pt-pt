---
title: Texturas
description: Fluxo de trabalho de recursos de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 27395fe377972f51c849f8a61f51a628612ed54d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202672"
---
# <a name="textures"></a>Texturas

As texturas são um [recurso partilhado](../concepts/lifetime.md)imutável. As texturas podem ser carregadas a partir do armazenamento de [bolhas](../how-tos/conversion/blob-storage.md) e aplicadas diretamente aos modelos, como demonstrado em [Tutorial: Mudar o ambiente e os materiais](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). No entanto, mais comummente, as texturas farão parte de um [modelo convertido,](../how-tos/conversion/model-conversion.md)onde são referenciadas pelos seus [materiais.](materials.md)

## <a name="texture-types"></a>Tipos de textura

Diferentes tipos de textura têm diferentes casos de uso:

* **Texturas 2D** são usadas principalmente em [materiais.](materials.md)
* **Os cubemaps** podem ser usados para o [céu.](../overview/features/sky.md)

## <a name="supported-texture-formats"></a>Formatos de textura suportados

Todas as texturas dadas ao ARR têm de estar no [formato DDS.](https://en.wikipedia.org/wiki/DirectDraw_Surface) De preferência com mipmaps e compressão de textura. Consulte [a ferramenta de linha de comando TexConv](../resources/tools/tex-conv.md) se pretender automatizar o processo de conversão.

## <a name="loading-textures"></a>Texturas de carregamento

Ao carregar uma textura, tem de especificar o tipo esperado. Se o tipo desajustar, a carga de textura falha.
Carregar uma textura com o mesmo URI duas vezes devolverá o mesmo objeto de textura, uma vez que é um [recurso partilhado.](../concepts/lifetime.md)

Semelhante aos modelos de carregamento, existem duas variantes de abordar um ativo de textura no armazenamento de bolhas de origem:

* O ativo de textura pode ser abordado pelo seu SAS URI. A função de carregamento relevante é `LoadTextureFromSASAsync` com `LoadTextureFromSASParams` parâmetro. Utilize esta variante também ao carregar [texturas incorporadas.](../overview/features/sky.md#built-in-environment-maps)
* A textura pode ser abordada diretamente pelos parâmetros de armazenamento do blob, caso o armazenamento do [blob esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante neste caso é `LoadTextureAsync` com parâmetro `LoadTextureParams` .

O seguinte código de amostra mostra como carregar uma textura através do seu SAS URI (ou textura incorporada) - note que apenas a função de carregamento/parâmetro difere para o outro caso:

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

Dependendo do que a textura deve ser usada, pode haver restrições para o tipo de textura e conteúdo. Por exemplo, o mapa de aspereza de um [material PBR](../overview/features/pbr-materials.md) deve ser em tons de cinza.

> [!CAUTION]
> Todas as funções *Async* em ARR retornam objetos de operação assíncronos. Deve guardar uma referência a esses objetos até que a operação esteja concluída. Caso contrário, o coletor de lixo C# pode apagar a operação mais cedo e nunca poderá terminar. No código de amostra acima da variável de membro '_textureLoad' é utilizado para manter uma referência até à chegada do evento *Concluído.*

## <a name="api-documentation"></a>Documentação da API

* [C# Classe de textura](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RemoteManager.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C# RemoteManager.LoadTextureFromSASAsync()](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [Classe de textura C++](/cpp/api/remote-rendering/texture)
* [C++ RemoteManager::LoadTextureAsync()](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C++ RemoteManager::LoadTextureFromSASAsync()](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>Passos seguintes

* [Materiais](materials.md)
* [Céu](../overview/features/sky.md)