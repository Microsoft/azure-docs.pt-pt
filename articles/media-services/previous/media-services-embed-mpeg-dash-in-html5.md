---
title: Incorporação de um vídeo de streaming adaptativo MPEG-DASH numa aplicação HTML5 com DASH.js [ Microsoft Docs
description: Este tópico demonstra como incorporar um vídeo de streaming adaptativo MPEG-DASH numa aplicação HTML5 com DASH.js.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 55c22a58ea76c268c40894c0ea64d43312b1ba27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269102"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Descrição geral
MPEG-DASH é um padrão ISO para o streaming adaptativo de conteúdos de vídeo, que oferece benefícios significativos para os desenvolvedores que querem fornecer uma saída de streaming de vídeo adaptativa de alta qualidade. Com o MPEG-DASH, o fluxo de vídeo ajusta-se automaticamente a uma definição mais baixa quando a rede fica congestionada. Isto reduz a probabilidade de o espectador ver um vídeo "pausado" enquanto o jogador descarrega os próximos segundos para jogar (isto é, reduz a probabilidade de tampão). À medida que o congestionamento da rede diminui, o leitor de vídeo, por sua vez, regressará a um fluxo de maior qualidade. Esta capacidade de adaptação da largura de banda necessária também resulta numa hora de início mais rápida para o vídeo. Isto significa que os primeiros segundos podem ser jogados num segmento de qualidade inferior para descarregar rapidamente e, em seguida, chegar a uma qualidade mais alta uma vez que o conteúdo suficiente tenha sido tamponado.

Dash.js é um leitor de vídeo MPEG-DASH de código aberto escrito em JavaScript. O seu objetivo é fornecer um jogador robusto e transversal que possa ser reutilizado livremente em aplicações que exijam reprodução de vídeo. Fornece reprodução MPEG-DASH em qualquer navegador que suporte as Extensões de Fonte de Mídia W3C (MSE), hoje em dia que é Chrome, Microsoft Edge e IE11 (outros navegadores indicaram a sua intenção de suportar MSE). Para mais informações sobre DASH.js, veja o repositório de dash.js GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Criar um leitor de vídeo de streaming baseado no navegador
Para criar uma página web simples que exiba um leitor de vídeo com os controlos esperados de tal reprodução, pausa, retrocesso, etc., é necessário:

1. Criar uma página HTML
2. Adicione a etiqueta de vídeo
3. Adicione o jogador de dash.js
4. Inicializar o jogador
5. Adicione um pouco de estilo CSS
6. Ver os resultados num navegador que implementa o MSE

A inicialização do leitor pode ser concluída em apenas um punhado de linhas de código JavaScript. Usando dash.js, é realmente tão simples incorporar vídeo MPEG-DASH nas suas aplicações baseadas no navegador.

## <a name="creating-the-html-page"></a>Criando a página HTML
O primeiro passo é criar uma página HTML padrão contendo o elemento **de vídeo,** guardar este ficheiro como basicPlayer.html, como ilustra o exemplo seguinte:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Adicionando o jogador de DASH.js
Para adicionar a dash.js implementação de referência à aplicação, é necessário agarrar o ficheiro dash.all.js da versão mais recente do projeto dash.js. Isto deve ser guardado na pasta JavaScript da sua aplicação. Este ficheiro é um ficheiro de conveniência que reúne todo o código dash.js necessário num único ficheiro. Se você tem um olhar ao redor do repositório dash.js, você encontra os ficheiros individuais, código de teste e muito mais, mas se tudo o que você quer fazer é usar dash.js, então o ficheiro dash.all.js é o que você precisa.

Para adicionar o leitor de dash.js às suas aplicações, adicione uma etiqueta de script na secção principal de basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Em seguida, crie uma função para inicializar o leitor quando a página estiver carregada. Adicione o seguinte script após a linha em que carrega dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Esta função cria primeiro um Texto DashCon. Isto é usado para configurar a aplicação para um ambiente de tempo de execução específico. Do ponto de vista técnico, define as classes que o quadro de injeção de dependência deve utilizar na construção da aplicação. Na maioria dos casos, usa o Dash.di.DashContext.

Em seguida, instantaneamente a classe primária do quadro dash.js, MediaPlayer. Esta classe contém os métodos principais necessários, tais como o jogo e a pausa, gere a relação com o elemento de vídeo e também gere a interpretação do ficheiro Media Presentation Description (MPD), que descreve o vídeo a ser reproduzido.

A função de arranque da classe MediaPlayer é chamada para garantir que o jogador está pronto para reproduzir vídeo. Entre outras coisas, a função garante que todas as classes necessárias (conforme definido pelo contexto) foram carregadas. Uma vez que o leitor esteja pronto, pode fixar o elemento de vídeo a ele utilizando a função attachView(). A função de arranque permite ao MediaPlayer injetar o fluxo de vídeo no elemento e também controlar a reprodução conforme necessário.

Passe o URL do ficheiro MPD para o MediaPlayer para que saiba sobre o vídeo que se espera reproduzir. A função de configuraçãoVideo() acaba de ser criada terá de ser executada uma vez que a página esteja completamente carregada. Faça-o utilizando o evento de carga do elemento do corpo. Mude o seu `<body>` elemento para:

```html
    <body onload="setupVideo()">
```

Por fim, desa ajuste o tamanho do elemento de vídeo utilizando o CSS. Num ambiente de streaming adaptativo, isso é especialmente importante porque o tamanho do vídeo que está a ser reproduzido pode mudar à medida que a reprodução se adapta às condições de rede em mudança. Nesta simples demonstração basta forçar o elemento de vídeo a ser 80% da janela do navegador disponível, adicionando o seguinte CSS à secção principal da página:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Reproduzindo um vídeo
Para reproduzir um vídeo, aponte o seu navegador para o ficheiro basicPlayback.htmi e clique em reproduzir o leitor de vídeo exibido.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também

[GitHub dash.js repositório](https://github.com/Dash-Industry-Forum/dash.js) 

