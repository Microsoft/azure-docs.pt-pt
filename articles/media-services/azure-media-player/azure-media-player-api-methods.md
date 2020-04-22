---
title: Métodos API do leitor de mídia Azure
description: O Azure Media Player API permite-lhe interagir com o vídeo através do JavaScript, quer o navegador esteja a reproduzir o vídeo através de vídeo HTML5, Flash, Silverlight ou qualquer outra tecnologia de reprodução suportada.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727271"
---
# <a name="api"></a>API #

O Azure Media Player API permite-lhe interagir com o vídeo através do JavaScript, quer o navegador esteja a reproduzir o vídeo através de vídeo HTML5, Flash, Silverlight ou qualquer outra tecnologia de reprodução suportada.

## <a name="referencing-the-player"></a>Referenciando o jogador ##

Para utilizar as funções API, precisa de acesso ao objeto do jogador. Felizmente é fácil de conseguir. Só tens de ter a certeza que a tua etiqueta de vídeo tem identificação. O código incorporado exemplo tem `vid1`uma identificação de . Se tiver vários vídeos numa página, certifique-se de que cada etiqueta de vídeo tem um ID único.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Se o jogador ainda não tiver sido inicializado através do atributo de configuração de dados ou de outro método, isso também irá inicializar o jogador.

## <a name="wait-until-the-player-is-ready"></a>Espere até o jogador estar pronto ##

O tempo que o Azure Media Player leva a configurar o vídeo e a API variará dependendo da tecnologia de reprodução que está a ser utilizada. HTML5 será muitas vezes muito mais rápido de carregar do que Flash ou Silverlight. Por essa razão, a função 'ready' do jogador deve ser utilizada para acionar qualquer código que exija a API do jogador.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OU

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Métodos API ##

Agora que tem acesso a um jogador pronto, pode controlar o vídeo, obter valores ou responder a eventos de vídeo. Os nomes da função API do Leitor de Mídia Azure tentam seguir a API dos meios de [comunicação HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). A principal diferença é que as funções getter/setter são usadas para propriedades de vídeo.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registo para eventos ##
Os eventos devem ser registados diretamente após a inicialização do jogador pela primeira vez para garantir que todos os eventos são devidamente comunicados à aplicação, e devem ser feitos fora do evento pronto.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Passos seguintes ##

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)