---
title: Azure Media Player Quickstart
description: Aprenda os passos básicos para configurar o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 1634731381be6ebbb8750746f88b8ff9e761025d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879195"
---
# <a name="azure-media-player-quickstart"></a>Início rápido do Leitor de Multimédia do Azure
O Azure Media Player é fácil de configurar. Leva apenas alguns minutos para obter a reprodução básica de conteúdos de mídia na sua conta Azure Media Services. Esta secção mostra os passos básicos sem entrar em detalhes. As secções que se seguem dão-lhe detalhes sobre como configurar e configurar o Azure Media Player.  Basta adicionar as seguintes inclusões ao `<head>` do seu documento

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **NÃO** deve utilizar a `latest` versão em produção, uma vez que esta está sujeita a alterações a pedido. Substitua `latest` por uma versão do Azure Media Player; por exemplo, `latest` substitua por `1.0.0` . As versões Azure Media Player podem ser consultadas a partir [daqui](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Use o elemento de vídeo

Em seguida, basta usar o `<video>` elemento como normalmente faria, mas com um atributo adicional `data-setup` contendo quaisquer opções. Estas opções podem incluir qualquer opção Azure Media Services num objeto JSON válido.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Se não quiser utilizar a configuração automática, pode omitir o `data-setup` atributo e rubricar manualmente um elemento de vídeo.

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

## <a name="next-steps"></a>Próximos passos ##

- [Azure Media Player Full Setup](./azure-media-player-full-setup.md)