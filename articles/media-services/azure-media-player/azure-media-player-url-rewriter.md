---
title: Azure Media Player URL Rewriter
description: O Azure Media Player irá reescrever um determinado URL da Azure Media Services para fornecer streams para SMOOTH, DASH, HLS v3 e HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: efa77ce7416b94331dce2bb4e7f77dd50c20d450
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448581"
---
# <a name="url-rewriter"></a>Reescritor de URL #

Por padrão, o Azure Media Player reescreverá um determinado URL da Azure Media Services para fornecer streams para SMOOTH, DASH, HLS v3 e HLS v4. Por exemplo, se a fonte for dada da seguinte forma, o Azure Media Player garantirá que tenta jogar todos os protocolos acima:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

No entanto, se desejar não utilizar o reescrita de URL, pode fazê-lo adicionando a `disableUrlRewriter` propriedade ao parâmetro. Isto significa que todas as informações que são transmitidas às fontes são diretamente transmitidas ao jogador sem modificação.  Aqui está um exemplo de adição de duas fontes ao leitor, no DASH e num Smooth Streaming.

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

Além disso, se quiser, pode especificar os formatos de streaming específicos que gostaria que o Azure Media Player reescrevesse para usar o `streamingFormats` parâmetro. As opções `DASH` incluem, , , , , . `SMOOTH` `HLSv3` `HLSv4` `HLS` . A diferença entre HLS e HLSv3 & v4 é que o formato HLS suporta a reprodução de conteúdo FairPlay. v3 e v4 não suportam FairPlay. Isto é útil se não tiver uma política de entrega para um determinado protocolo disponível.  Aqui está um exemplo de quando um protocolo DASH não está ativado com o seu ativo.

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
> As informações de proteção de toda a largura apenas persistem no protocolo DASH.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)