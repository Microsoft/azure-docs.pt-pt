---
title: Preparação de ficheiros para conversão
description: Recomendações sobre a melhor forma de colocar ficheiros no recipiente de entrada.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 9a094755dfb9381b1e4d6abdf1c0e6342d7427c1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308444"
---
# <a name="laying-out-files-for-conversion"></a>Preparação de ficheiros para conversão

Para processar corretamente um ativo, o serviço de conversão precisa de ser capaz de encontrar todos os ficheiros de entrada.
Estes consistem no principal ficheiro de ativos que está a ser convertido e geralmente em alguns outros ficheiros referenciados por caminhos dentro do ficheiro do ativo.
O pedido de conversão de um ativo é dado dois parâmetros que determinam como o serviço de conversão encontra estes ficheiros: O `settings.inputLocation.blobPrefix` (que é opcional) e o `settings.inputLocation.relativeInputAssetPath` .
Estão totalmente documentados na página [API do REST de Conversão.](conversion-rest-api.md)
Para efeitos de colocação de ficheiros, o importante é notar que o `BlobPrefix` conjunto completo de ficheiros que estão disponíveis para o serviço de conversão no processamento do ativo.

> [!Note]
> O serviço irá descarregar todos os ficheiros sob a entrada. BlobPrefix. Certifique-se de que os nomes e caminhos dos ficheiros não excedem os [limites](https://docs.microsoft.com/windows/win32/fileio/maximum-file-path-limitation) de comprimento do percurso do Windows para evitar problemas no serviço. 

## <a name="placing-files-so-they-can-be-found"></a>Colocação de ficheiros para que possam ser encontrados

Quando um ativo de origem utiliza ficheiros externos, os caminhos para esses ficheiros serão armazenados dentro do ativo.
O serviço de conversão tem de interpretar estes caminhos num sistema de ficheiros diferente do sistema de ficheiros original do ativo.
Se os caminhos forem armazenados como caminhos relativos e a localização relativa entre o ativo de origem e o ficheiro que refere é inalterado, então é fácil para o serviço de conversão encontrar o ficheiro referenciado.

> [!Note]
> Recomendamos que coloque ficheiros no recipiente de entrada para que as localizações relativas dos ficheiros sejam as mesmas que eram quando o ativo foi criado.

> [!Note]
> Prefere criar ativos que transportem caminhos relativos.
> O tutorial sobre [a configuração de materiais para 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) dá um exemplo 3ds Max de como garantir que um ativo usa caminhos relativos.

## <a name="finding-textures"></a>Encontrar texturas

Devido às muitas formas como os ativos podem ser gerados, o serviço de conversão tem de ser flexível.
Em particular, tem de lidar com situações em que os caminhos do ativo e a localização das texturas não correspondem precisamente.
Um exemplo é quando os ativos são gerados contendo caminhos absolutos, uma vez que estes caminhos nunca corresponderão ao sistema de ficheiros utilizado pelo serviço de conversão.
Para lidar com esta situação entre outros, usamos uma melhor abordagem de esforço para encontrar texturas.

O algoritmo para localizar texturas é o seguinte: Dado um caminho como armazenado num ativo, encontre o sufixo de sub-caminho mais longo que, quando usado como um caminho relativo a partir da localização do ativo de origem, visa um ficheiro que existe.
Se nenhum sub-caminho (incluindo todo o caminho) tiver como alvo um ficheiro, a textura é considerada como desaparecida.

Considere o seguinte sistema de ficheiros inventado: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Se o myAsset.fbx referenciar uma textura com o caminho `..\Textures\MyAssetTextures\myTexture.png` relativo, então o serviço de conversão utilizará o ficheiro E. Na verdade, pode usar qualquer um dos ficheiros A, C e E se existirem, sendo o ficheiro E preferido uma vez que é encontrado com o sufixo mais longo.
Os ficheiros B e D nunca serão `Textures\myTexture.png` utilizados, porque não fazem parte de qualquer sufixo do caminho armazenado.
Se o ativo, em vez disso, contiver os caminhos `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` ou, então o serviço de conversão será capaz de encontrar os ficheiros A e C se existirem (preferindo C em A). No entanto, E não pode ser encontrado desta forma, e o ficheiro terá de ser recolocado.
Isto poderia ser corrigido movendo a pasta Texturas ao lado do ativo.

> [!Note]
> Se as texturas não forem encontradas, um possível remédio é garantir que o ativo é um irmão de alguma subtree que contém as texturas.

## <a name="next-steps"></a>Passos seguintes

- [Conversão de modelo](model-conversion.md)
