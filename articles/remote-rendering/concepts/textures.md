---
title: Texturas
description: Fluxo de trabalho de recursos de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594422"
---
# <a name="textures"></a>Texturas

As texturas são um [recurso partilhado](../concepts/lifetime.md)imutável. As texturas podem ser carregadas a partir do armazenamento de [bolhas](../how-tos/conversion/blob-storage.md) e aplicadas diretamente aos modelos, como demonstrado em [Tutorial: Mudar o ambiente e os materiais](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). No entanto, mais comummente, as texturas farão parte de um [modelo convertido,](../how-tos/conversion/model-conversion.md)onde são referenciadas pelos seus [materiais.](materials.md)

## <a name="texture-types"></a>Tipos de textura

Diferentes tipos de textura têm diferentes casos de uso:

* **Texturas 2D** são usadas principalmente em [materiais.](materials.md)
* **Os cubemaps** podem ser usados para o [céu.](../overview/features/sky.md)

## <a name="supported-texture-formats"></a>Formatos de textura suportados

Todas as texturas dadas ao ARR têm de estar no [formato DDS.](https://en.wikipedia.org/wiki/DirectDraw_Surface) De preferência com mipmaps e compressão de textura.

## <a name="loading-textures"></a>Texturas de carregamento

Ao carregar uma textura, tem de especificar o tipo esperado. Se o tipo desajustar, a carga de textura falha.
Carregar uma textura com o mesmo URI duas vezes devolverá o mesmo objeto de textura, uma vez que é um [recurso partilhado.](../concepts/lifetime.md)

Semelhante aos modelos de carregamento, existem duas variantes de abordar um ativo de textura no armazenamento de bolhas de origem:

* A textura pode ser abordada diretamente pelos parâmetros de armazenamento do blob, caso o armazenamento do [blob esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carregamento relevante neste caso é `LoadTextureAsync` com parâmetro `LoadTextureOptions` .
* O ativo de textura pode ser abordado pelo seu SAS URI. A função de carregamento relevante é `LoadTextureFromSasAsync` com `LoadTextureFromSasOptions` parâmetro. Utilize esta variante também ao carregar [texturas incorporadas.](../overview/features/sky.md#built-in-environment-maps)

O seguinte código de amostra mostra como carregar uma textura:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Note que em caso de utilização da sua variante SAS apenas a função de carregamento/parâmetro difere.

Dependendo do que a textura deve ser usada, pode haver restrições para o tipo de textura e conteúdo. Por exemplo, o mapa de aspereza de um [material PBR](../overview/features/pbr-materials.md) deve ser em tons de cinza.

## <a name="api-documentation"></a>Documentação da API

* [C# Classe de textura](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection.LoadTextureAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection.LoadTextureFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Classe de textura C++](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection::LoadTextureAsync()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection::LoadTextureFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Passos seguintes

* [Materiais](materials.md)
* [Céu](../overview/features/sky.md)