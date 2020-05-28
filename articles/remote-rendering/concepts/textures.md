---
title: Texturas
description: Fluxo de trabalho de recursos de textura
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: de3f127d97803ea920d61d748a1af0c80a1a1afc
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759137"
---
# <a name="textures"></a>Texturas

Texturas são um [recurso partilhado](../concepts/lifetime.md)imutável. As texturas podem ser carregadas a partir do [armazenamento de blob](../how-tos/conversion/blob-storage.md) e aplicadas diretamente aos modelos, como demonstra o [Tutorial: Mudar o ambiente e os materiais.](../tutorials/unity/changing-environment-and-materials.md) No entanto, mais comummente, as texturas farão parte de um [modelo convertido,](../how-tos/conversion/model-conversion.md)onde são referenciadas pelos seus [materiais.](materials.md)

## <a name="texture-types"></a>Tipos de textura

Diferentes tipos de textura têm diferentes casos de uso:

* **Texturas 2D** são usadas principalmente em [materiais](materials.md).
* **Os cubemaps** podem ser usados para o [céu.](../overview/features/sky.md)

## <a name="supported-texture-formats"></a>Formatos de textura suportados

Todas as texturas dadas à ARR têm de estar em [formato DDS.](https://en.wikipedia.org/wiki/DirectDraw_Surface) De preferência com ipmaps e compressão de textura. Consulte [a ferramenta de linha de comando TexConv](../resources/tools/tex-conv.md) se pretender automatizar o processo de conversão.

## <a name="loading-textures"></a>Texturas de carregamento

Ao carregar uma textura, tem de especificar o seu tipo esperado. Se o tipo desina desajuste, a carga de textura falha.
Carregar uma textura com o mesmo URI duas vezes devolverá o mesmo objeto de textura, uma vez que é um [recurso partilhado.](../concepts/lifetime.md)

Semelhantes aos modelos de carregamento, existem duas variantes de abordar um ativo de textura no armazenamento de bolhas de origem:

* O ativo de textura pode ser abordado pelo seu SAS URI. A função de carga relevante é `LoadTextureFromSASAsync` com parâmetro `LoadTextureFromSASParams` . Utilize esta variante também ao carregar [texturas incorporadas.](../overview/features/sky.md#built-in-environment-maps)
* A textura pode ser abordada diretamente por parâmetros de armazenamento de bolhas, caso o [armazenamento de bolhas esteja ligado à conta](../how-tos/create-an-account.md#link-storage-accounts). A função de carga relevante neste caso é `LoadTextureAsync` com parâmetro `LoadTextureParams` .

O seguinte código de amostra mostra como carregar uma textura através do seu SAS URI (ou textura incorporada) - note que apenas a função/parâmetro de carregamento difere para o outro caso:

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
        if (res->IsRanToCompletion())
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


Dependendo do que a textura deve ser usada, pode haver restrições para o tipo de textura e conteúdo. Por exemplo, o mapa de rugosidade de um [material PBR](../overview/features/pbr-materials.md) deve ser de escala cinzenta.

> [!CAUTION]
> Todas as funções *De Sincronia* em ARR devolvem objetos de operação assíncronos. Deve armazenar uma referência a esses objetos até que a operação esteja concluída. Caso contrário, o coletor de lixo C# pode apagar a operação mais cedo e nunca poderá terminar. No código de amostra acima da variável membro '_textureLoad' é utilizado para manter uma referência até à chegada do evento *concluído.*

## <a name="next-steps"></a>Próximos passos

* [Materiais](materials.md)
* [Céu](../overview/features/sky.md)
