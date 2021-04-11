---
title: Como usar o jogador Shaka com a Azure Media Services
description: Este artigo explica como usar o jogador Shaka com a Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9699e6ad3f004f94c83440dd39f13c6db887e224
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281953"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Como usar o jogador Shaka com a Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Descrição Geral

Shaka Player é uma biblioteca JavaScript de código aberto para meios adaptativos. Reproduz formatos de mídia adaptativo (como DASH e HLS) num browser, sem usar plugins ou Flash. Em vez disso, o Jogador Shaka utiliza as extensões de origem de mídia abertas e extensões de mídia encriptadas.

Recomendamos a utilização [ deMux.js, ](https://github.com/videojs/mux.js/) pois, sem ele, o jogador Shaka apoiaria o formato HLS CMAF, mas não o HLS TS.

A documentação oficial pode ser encontrada na [documentação do jogador shaka](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Código de exemplo

O código de amostra deste artigo está disponível em [amostras Azure-Samples/media-services-3ºparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementação do jogador

Siga estas instruções se precisar de implementar a sua própria instância do leitor:

1. Crie um `index.html` ficheiro onde irá hospedar o leitor. Adicione as seguintes linhas de código (pode substituir as versões para mais recentes, se aplicável):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Adicione um ficheiro JavaScript com o seguinte código:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. `manifestUrl`Substitua-se pelo URL HLS ou DASH do localizador de streaming do seu ativo, que pode ser encontrado na página de localizador de streaming no portal Azure.
    ![URLs localizadores de streaming](media/player-shaka-player-how-to/streaming-urls.png)

1. Executar um servidor (por exemplo `npm http-server` com) e o seu jogador deve estar a trabalhar...

## <a name="set-up-captions"></a>Configurar legendas

### <a name="set-up-vod-captions"></a>Configurar legendas VOD

Executar as seguintes linhas de código e substituir `captionUrl` pelo seu diretório .vtt (ficheiro vtt precisa estar no mesmo anfitrião para evitar erros CORS), com o código de `lang` duas letras para o idioma, e `type` com qualquer um ou `caption` `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Configurar legendas de transmissão ao vivo

Ativar as legendas em live stream está configurada adicionando a seguinte linha de código:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Configurar a autenticação simbólica

Executar as seguintes linhas de código e substituir `token` por sua cadeia simbólica:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Configurar encriptação AES-128

O Shaka Player não suporta atualmente a encriptação AES-128.

Um link para um [problema](https://github.com/google/shaka-player/issues/850) do GitHub para seguir o estado desta funcionalidade.

## <a name="set-up-drm-protection"></a>Criar proteção DRM

Shaka Player usa extensões de mídia encriptadas (EME), que requer um URL seguro para usar. Assim, para testar qualquer conteúdo protegido drm é necessário usar https. Se o site estiver a utilizar https, então o manifesto e cada segmento também terão de usar https. Isto deve-se aos requisitos de conteúdo misto.

A ordem de preferência para a gestão shaka dos URL(s) dos seus servidores de licenças):

1. ClearKey config, usado para depurar, deve anular todo o resto. (A aplicação ainda pode especificar um servidor de licença ClearKey.)
2. Os servidores configurados por aplicações, se houver algum, devem substituir qualquer coisa do manifesto.
3. Os servidores de licença fornecidos pelo manifesto só são utilizados se nada mais for especificado.

Para especificar o URL do servidor de licença para Widevine ou PlayReady, podemos usar o seguinte código:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Todo o conteúdo do FairPlay requer a definição de um certificado de servidor. É definido na configuração do Jogador:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Para obter mais informações, consulte [a documentação de proteção drm do jogador Shaka](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Passos seguintes

* [Use o Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Quickstart: Encriptar conteúdo](drm-encrypt-content-how-to.md)
