---
title: Reescritor de URL do leitor de mídia Azure
description: O Azure Media Player reescreverá um dado URL da Azure Media Services para fornecer streams para SMOOTH, DASH, HLS v3 e HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726460"
---
# <a name="url-rewriter"></a>Reescritor de URL #

Por padrão, o Azure Media Player reescreverá um dado URL da Azure Media Services para fornecer streams para SMOOTH, DASH, HLS v3 e HLS v4. Por exemplo, se a fonte for dada da seguinte forma, o Azure Media Player garantirá que tenta jogar todos os protocolos acima referidos:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

No entanto, se desejar não utilizar o reescritor url, `disableUrlRewriter` pode fazê-lo adicionando a propriedade ao parâmetro. Isto significa que todas as informações que são transmitidas às fontes são transmitidas diretamente ao jogador sem modificação.  Aqui está um exemplo de adição de duas fontes ao leitor, no DASH e num Smooth Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

ou

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Além disso, se desejar, pode especificar os formatos de streaming específicos `streamingFormats` que gostaria que o Azure Media Player reescrevesse para utilizar o parâmetro. As `DASH`opções `HLSv3` `HLSv4`incluem, `HLS` `SMOOTH`, , . A diferença entre HLS e HLSv3 & v4 é que o formato HLS suporta a reprodução de conteúdos fairPlay. v3 e v4 não suportam FairPlay. Isto é útil se não tiver uma política de entrega para um determinado protocolo disponível.  Aqui está um exemplo de quando um protocolo DASH não está ativado com o seu ativo.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

ou

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Os dois acima podem ser usados em combinação entre si para múltiplas circunstâncias com base no seu ativo particular.

> [!NOTE]
> A informação sobre a proteção da grande variedade apenas persiste no protocolo DASH.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)