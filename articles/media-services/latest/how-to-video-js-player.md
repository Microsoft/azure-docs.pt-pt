---
title: Use o jogador de Video.js com a Azure Media Services
description: Este artigo explica como usar o objeto de vídeo HTML e JavaScript com Azure Media Services
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
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954585"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Como utilizar o jogador Video.js com a Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Descrição geral

Video.js é um leitor de vídeo sonoro construído para um mundo HTML5. Reproduz formatos de mídia adaptativo (como DASH e HLS) num browser, sem usar plugins ou Flash. Em vez disso, Video.js utiliza as extensões de MediaSource e extensões de mídia encriptadas. Além disso, suporta a reprodução de vídeo em desktops e dispositivos móveis.

A sua documentação oficial pode ser consultada em [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Código de exemplo
O código de amostra deste artigo está disponível em [amostras Azure-Samples/media-services-3ºparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementar o jogador

1. Crie um `index.html` ficheiro onde irá hospedar o leitor. Adicione as seguintes linhas de código (pode substituir as versões para mais recentes, se aplicável):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Adicione um `index.js` ficheiro com o seguinte código:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. `manifestUrl`Substitua-se pelo URL HLS ou DASH do localizador de streaming do seu ativo que pode ser encontrado na página de localizador de streaming no portal Azure.
    ![URLs localizadores de streaming](media/how-to-shaka-player/streaming-urls.png)

4. `protocolType`Substitua-as pelas seguintes opções:

    - "aplicação/x-mpegURL" para protocolos HLS
    - "aplicação/dash+xml" para protocolos DASH

### <a name="set-up-captions"></a>Configurar legendas

Executar o `addRemoteTextTrack` método e substituir:

- `subtitleKind` com qualquer um `"captions"` `"subtitles"` , , `"descriptions"` ou `"metadata"`  
- `caption` com o caminho do ficheiro .vtt (o ficheiro VTT precisa estar no mesmo hospedeiro para evitar erros cors)
- `subtitleLang` com o código BCP 47 para idioma, por exemplo, `"eng"` para inglês ou `"es"` espanhol
- `subtitleLabel` com o seu nome de exibição desejado da legenda

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Configurar a autenticação simbólica

O sinal deve ser definido no campo de autorização do cabeçalho do pedido. Para evitar problemas com o CORS, este token deve ser definido apenas nos pedidos com `'keydeliver'` url. As seguintes linhas de código devem fazer o trabalho:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Em seguida, a função acima deve ser anexada ao `videojs.Hls.xhr.beforeRequest` evento.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Configurar encriptação AES-128

Video.js suporta encriptação AES-128 sem qualquer configuração adicional. 

> [!NOTE] 
> Existe atualmente um [problema](https://github.com/videojs/video.js/issues/6717) com encriptação e conteúdo HLS/DASH CMAF, que não são jogáveis.

### <a name="set-up-drm-protection"></a>Criar proteção DRM

Para apoiar a proteção drm, deve adicionar a extensão oficial [videojs-contrib-eme.](https://github.com/videojs/videojs-contrib-eme) Uma versão CDN também funciona.

1. No `index.js` ficheiro acima descrito, deve rubricar a extensão EME adicionando `videoJS.eme();` *antes* de adicionar a origem do vídeo:

   ```javascript
    videoJS.eme();
   ```

2. Agora pode definir os URLs dos serviços drm, e os URLs das licenças correspondentes da seguinte forma:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Aquisição do URL de licença

Para adquirir o URL de licença, pode:

- Consulte a configuração do seu fornecedor DRM
- ou, se estiver a utilizar a amostra, consulte o `output.json` documento gerado quando executou osetup-vod.ps1script PowerShell para VODs, ou o script *start-live.ps1* para streams ao vivo.  Também encontrará os KIDs dentro deste ficheiro.

#### <a name="using-tokenized-drm"></a>Utilização de DRM tokenized

Para suportar a proteção tokenized DRM, tem de adicionar a seguinte linha à `src` propriedade do jogador:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Passos seguintes

- [Use o Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Quickstart: Encriptar conteúdo](encrypt-content-quickstart.md)
