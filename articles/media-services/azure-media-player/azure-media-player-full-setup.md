---
title: Azure Media Player Full Setup
description: Saiba como configurar o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 15f5918748df80cec01ccf89835a0ef51da64529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296251"
---
# <a name="azure-media-player-full-setup"></a>Configuração completa do Leitor de Multimédia do Azure #

O Azure Media Player é fácil de configurar. Bastam alguns momentos para obter a reprodução básica de conteúdos de mídia na sua conta Azure Media Services. [As amostras](https://github.com/Azure-Samples/azure-media-player-samples) também são fornecidas no diretório de amostras da libertação.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Passo 1: Incluir os ficheiros JavaScript e CSS na cabeça da sua página ##

Com o Azure Media Player, pode aceder aos scripts a partir da versão hospedada pela CDN. Muitas vezes é recomendado agora colocar JavaScript antes da etiqueta de corpo final `<body>` em vez do , mas O `<head>` Azure Media Player inclui um 'HTML5 Shiv', que precisa estar na cabeça para versões IE mais antigas para respeitar a etiqueta de vídeo como um elemento válido.

> [!NOTE]
> Se já estiver a utilizar um shiv HTML5 como [o Modernizr,](https://modernizr.com/) pode incluir o JavaScript do Azure Media Player em qualquer lugar. No entanto, certifique-se de que a sua versão de Modernizr inclui o shiv para vídeo.

### <a name="cdn-version"></a>Versão CDN ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **NÃO** deve utilizar a `latest` versão em produção, uma vez que esta está sujeita a alterações a pedido. Substitua `latest` por uma versão do Azure Media Player. Por exemplo, `latest` substitua-o por `2.1.1` . As versões Azure Media Player podem ser consultadas a partir [daqui](azure-media-player-changelog.md).

> [!NOTE]
> Desde o `1.2.0` lançamento, já não é necessário incluir a localização para os técnicos de retorno (irá automaticamente recolher a localização a partir do caminho relativo do ficheiro azuremediaplayer.min.js). Pode modificar a localização dos técnicos de retorno adicionando o seguinte script nos `<head>` scripts acima referidos.

> [!NOTE]
> Devido à natureza dos plugins Flash e Silverlight, os ficheiros SWF e XAP devem ser alojados num domínio sem qualquer informação ou dados sensíveis - isto é automaticamente tratado por si com a versão acolhida pelo CDN Azure.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Passo 2: Adicione uma etiqueta de vídeo HTML5 à sua página ##

Com o Azure Media Player, pode utilizar uma video tag HTML5 para incorporar um vídeo. O Azure Media Player irá então ler a etiqueta e fazê-la funcionar em todos os navegadores, e não apenas aqueles que suportam o vídeo HTML5. Para além da marcação básica, o Azure Media Player precisa de algumas peças extra.

1. O `<data-setup>` atributo no `<video>` Azure Media Player diz ao Azure Media Player para configurar automaticamente o vídeo quando a página estiver pronta, e ler qualquer (em formato JSON) a partir do atributo.
1. O `id` atributo: Deve ser usado e único para cada vídeo na mesma página.
1. O `class` atributo contém duas classes:
    - `azuremediaplayer` aplica estilos que são necessários para a funcionalidade UI do Azure Media Player
    - `amp-default-skin` aplica a pele padrão aos controlos HTML5
1. O `<source>` inclui dois atributos necessários
    - `src` o atributo pode incluir um ficheiro **.ism/manifesto* da Azure Media Services, a Azure Media Player adiciona automaticamente os URLs para DASH, SMOOTH e HLS ao jogador
    - `type` atributo é o tipo MIME necessário do fluxo. O tipo MIME associado a *".ism/manifest"* é *"application/vnd.ms-sstr+xml"*
1. O atributo *opcional* `<data-setup>` no tells `<source>` Azure Media Player se houver alguma política de entrega única para o stream da Azure Media Services, incluindo, mas não limitado a, tipo de encriptação (AES ou PlayReady, Widevine ou FairPlay) e token.

Inclua/exclua atributos, configurações, fontes e faixas exatamente como faria para o vídeo HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Por predefinição, o botão de reprodução grande está localizado no canto superior esquerdo para não cobrir as partes interessantes do cartaz. Se preferir centrar o botão de reprodução grande, pode adicionar um adicional `amp-big-play-centered` `class` ao seu `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Configuração alternativa para HTML carregado dinamicamente ###

Se a sua página web ou aplicação carregar a etiqueta de vídeo de forma dinâmica (ajax, apendchild, etc.), para que possa não existir quando a página estiver carregada, irá querer configurar manualmente o leitor em vez de se basear no atributo de configuração de dados. Para isso, remova primeiro o atributo de configuração de dados da etiqueta para que não haja confusão quando o leitor é inicializado. Em seguida, execute o JavaScript seguinte algum tempo depois do JavaScript Azure Media Player ter carregado, e depois da tag de vídeo ter sido carregada no DOM.

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

O primeiro argumento na `amp` função é a identificação da sua etiqueta de vídeo. Substitua-o pelo seu.

O segundo argumento é um objeto de opções. Permite-lhe definir opções adicionais como pode com o atributo de configuração de dados.

O terceiro argumento é uma chamada "pronta". Uma vez inicializado o Azure Media Player, chamará esta função. Na chamada pronta, o objeto 'this' refere-se à instância do jogador.

Em vez de usar um ID de elemento, também pode passar uma referência ao próprio elemento.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)