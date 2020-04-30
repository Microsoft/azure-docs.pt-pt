---
title: Azure Media Player Quickstart
description: Aprenda os passos básicos para configurar o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726467"
---
# <a name="azure-media-player-quickstart"></a>Início rápido do Leitor de Multimédia do Azure
Azure Media Player é fácil de configurar. Leva apenas alguns minutos para obter a reprodução básica de conteúdos de mídia na sua conta Azure Media Services. Esta secção mostra os passos básicos sem entrar em detalhes. As secções que se seguem dão-lhe detalhes sobre como configurar e configurar o Azure Media Player.  Basta adicionar as seguintes inclusões ao `<head>` do seu documento

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **Não** deve utilizar `latest` a versão em produção, uma vez que esta está sujeita a alterações a pedido. Substitua `latest` por uma versão do Azure Media Player; por exemplo `latest` `1.0.0`substituir por . As versões Azure Media Player podem ser consultadas a partir [daqui.](azure-media-player-changelog.md)

## <a name="use-the-video-element"></a>Use o elemento de vídeo

Em seguida, `<video>` basta usar o elemento como `data-setup` normalmente faria, mas com um atributo adicional contendo quaisquer opções. Estas opções podem incluir qualquer opção Azure Media Services num objeto JSON válido.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Se não quiser utilizar a configuração automática, pode omitir o `data-setup` atributo e inicializar manualmente um elemento de vídeo.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)