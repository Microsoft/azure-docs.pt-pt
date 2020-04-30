---
title: Configuração completa do jogador de mídia azure
description: Aprenda a configurar o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727245"
---
# <a name="azure-media-player-full-setup"></a>Configuração completa do Leitor de Multimédia do Azure #

Azure Media Player é fácil de configurar. Basta maquete sem estôver os conteúdos dos media logo na sua conta Azure Media Services. [As amostras](https://github.com/Azure-Samples/azure-media-player-samples) também são fornecidas no diretório de amostras da libertação.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Passo 1: Incluir os ficheiros JavaScript e CSS na cabeça da sua página ##

Com o Azure Media Player, podeaceder aos scripts a partir da versão hospedada da CDN. É frequentemente recomendado agora colocar javaScript antes `<body>` da `<head>`etiqueta do corpo final em vez da , mas O Azure Media Player inclui um 'HTML5 Shiv', que precisa estar na cabeça para versões IE mais antigas para respeitar a etiqueta de vídeo como um elemento válido.

> [!NOTE]
> Se já estiver a usar uma faca HTML5 como [modernizr,](http://modernizr.com/) pode incluir o JavaScript do Jogador de Mídia Azure em qualquer lugar. No entanto, certifique-se de que a sua versão do Modernizr inclui a faca para vídeo.

### <a name="cdn-version"></a>Versão CDN ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> **Não** deve utilizar `latest` a versão em produção, uma vez que esta está sujeita a alterações a pedido. Substitua `latest` por uma versão do Azure Media Player. Por exemplo, `latest` `2.1.1`substitua-o por . As versões Azure Media Player podem ser consultadas a partir [daqui.](azure-media-player-changelog.md)

> [!NOTE]
> Desde `1.2.0` o lançamento, já não é necessário incluir a localização para as tecnologias de retorno (irá automaticamente captar a localização a partir do caminho relativo do ficheiro azuremediaplayer.min.js). Pode modificar a localização dos técnicos de recuo adicionando o seguinte script nos scripts `<head>` seguintes.

> [!NOTE]
> Devido à natureza dos plugins Flash e Silverlight, os ficheiros swf e xap devem ser alojados num domínio sem qualquer informação ou dados sensíveis - isto é automaticamente tratado para si com a versão hospedada do Azure CDN.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Passo 2: Adicione uma etiqueta de vídeo HTML5 à sua página ##

Com o Azure Media Player, pode utilizar uma etiqueta de vídeo HTML5 para incorporar um vídeo. O Azure Media Player irá então ler a etiqueta e fazê-la funcionar em todos os navegadores, e não apenas nos que suportam o vídeo HTML5. Para além da marcação básica, o Azure Media Player precisa de algumas peças extras.

1. O `<data-setup>` atributo `<video>` no diz ao Azure Media Player para configurar automaticamente o vídeo quando a página estiver pronta, e ler qualquer (em formato JSON) do atributo.
1. O `id` atributo: Deve ser utilizado e único para cada vídeo na mesma página.
1. O `class` atributo contém duas classes:
    - `azuremediaplayer`aplica estilos que são necessários para a funcionalidade UI do Azure Media Player
    - `amp-default-skin`aplica a pele padrão aos controlos HTML5
1. O `<source>` inclui dois atributos necessários
    - `src`atributo pode incluir um ficheiro **.ism/manifesto* da Azure Media Services é adicionado, Azure Media Player adiciona automaticamente os URLs para DASH, SMOOTH e HLS ao leitor
    - `type`atributo é o tipo MIME necessário do fluxo. O tipo MIME associado a *".ism/manifest"* é *"application/vnd.ms-sstr+xml"*
1. O *optional* `<data-setup>` atributo opcional `<source>` no conta Azure Media Player se existem políticas de entrega únicas para o stream da Azure Media Services, incluindo, mas não se limitando a, tipo de encriptação (AES ou PlayReady, Widevine ou FairPlay) e ficha.

Inclua/exclua atributos, configurações, fontes e faixas exatamente como faria para o vídeo HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Por padrão, o botão de reprodução grande está localizado no canto superior esquerdo para que não cubra as partes interessantes do cartaz. Se preferir centrar o botão de reprodução grande, pode adicionar um adicional `amp-big-play-centered` `class` ao seu `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Configuração alternativa para HTML dinamicamente carregada ###

Se a sua página web ou aplicação carregar a etiqueta de vídeo de forma dinâmica (ajax, apêndiceChild, etc.), para que possa não existir quando a página for carregada, irá querer configurar manualmente o leitor em vez de confiar no atributo de configuração de dados. Para isso, primeiro remova o atributo de configuração de dados da etiqueta para que não haja confusão em torno de quando o jogador é inicializado. Em seguida, execute o seguinte JavaScript algum tempo depois do JavaScript do Jogador de Mídia Azure ter carregado, e depois da etiqueta de vídeo ter sido carregada no DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

O primeiro argumento `amp` na função é a identificação da sua etiqueta de vídeo. Substitua-o pelo seu.

O segundo argumento é um objeto de opções. Permite-lhe definir opções adicionais como pode com o atributo de configuração de dados.

O terceiro argumento é uma chamada "pronta". Uma vez que o Azure Media Player tenha sido inicializado, chamará a esta função. No backback pronto, o objeto 'this' refere-se à instância do jogador.

Em vez de utilizar um ID de elemento, também pode passar uma referência ao próprio elemento.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)