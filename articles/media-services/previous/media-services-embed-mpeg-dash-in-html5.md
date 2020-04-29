---
title: Incorporar um Vídeo de Streaming Adaptativo MPEG-DASH numa aplicação HTML5 com DASH.js / Microsoft Docs
description: Este tópico demonstra como incorporar um Vídeo de Streaming Adaptativo MPEG-DASH numa aplicação HTML5 com DASH.js.
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
ms.openlocfilehash: 6c1df14ba5a9f233f42750d4e6dea68a7d6ddc0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77564861"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um Vídeo de Transmissão em fluxo Adaptivo MPEG-DASH numa Aplicação HTML5 com DASH.js  

## <a name="overview"></a>Descrição geral
O MPEG-DASH é um padrão ISO para o streaming adaptativo de conteúdos de vídeo, que oferece benefícios significativos para os desenvolvedores que querem fornecer uma saída de streaming de vídeo adaptável de alta qualidade. Com o MPEG-DASH, o fluxo de vídeo ajusta-se automaticamente a uma definição mais baixa quando a rede fica congestionada. Isto reduz a probabilidade de o espectador ver um vídeo "pausado" enquanto o leitor descarrega os próximos segundos para jogar (isto é, reduz a probabilidade de tampão). À medida que o congestionamento da rede diminui, o leitor de vídeo regressará, por sua vez, a um fluxo de maior qualidade. Esta capacidade de adaptação da largura de banda necessária também resulta num tempo de início mais rápido para o vídeo. Isto significa que os primeiros segundos podem ser jogados num segmento de qualidade mais baixa e, em seguida, subir para uma qualidade mais alta uma vez que o conteúdo suficiente foi tamponado.

Dash.js é um leitor de vídeo MPEG-DASH de código aberto escrito no JavaScript. O seu objetivo é fornecer um jogador robusto e cross-platform que possa ser livremente reutilizado em aplicações que requerem reprodução de vídeo. Fornece reprodução MPEG-DASH em qualquer navegador que suporte as Extensões de Fonte de Mídia W3C (MSE), hoje que é Chrome, Microsoft Edge e IE11 (outros navegadores indicaram a sua intenção de suportar mse). Para mais informações sobre o DASH.js, js consulte o repositório GitHub dash.js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Criar um leitor de vídeo de streaming baseado no navegador
Para criar uma página web simples que exibe um leitor de vídeo com os controlos esperados tal jogada, pausa, rebobinar, etc., é necessário:

1. Criar uma página HTML
2. Adicione a etiqueta de vídeo
3. Adicione o leitor dash.js
4. Inicializar o jogador
5. Adicione um pouco de estilo CSS
6. Veja os resultados num navegador que implementa a MSE

Inicializar o leitor pode ser completado em apenas um punhado de linhas de código JavaScript. Usando dash.js, é realmente tão simples incorporar vídeo MPEG-DASH nas suas aplicações baseadas no navegador.

## <a name="creating-the-html-page"></a>Criando a página HTML
O primeiro passo é criar uma página HTML padrão contendo o elemento de **vídeo,** guardar este ficheiro como basicPlayer.html, como ilustra o seguinte exemplo:

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

## <a name="adding-the-dashjs-player"></a>Adicionar o jogador DASH.js
Para adicionar a implementação de referência dash.js à aplicação, você precisa pegar o ficheiro dash.all.js da versão mais recente do projeto dash.js. Isto deve ser guardado na pasta JavaScript da sua aplicação. Este ficheiro é um ficheiro de conveniência que reúne todo o código dash.js necessário num único ficheiro. Se você olhar ao redor do repositório dash.js, você encontra os ficheiros individuais, código de teste e muito mais, mas se tudo o que você quer fazer é usar dash.js, então o ficheiro dash.all.js é o que você precisa.

Para adicionar o leitor dash.js às suas aplicações, adicione uma etiqueta de script à secção principal do basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Em seguida, crie uma função para inicializar o leitor quando a página estiver carregada. Adicione o seguinte script após a linha em que carrega o traço.all.js:

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

Esta função cria primeiro um DashContext. Isto é usado para configurar a aplicação para um ambiente de tempo de execução específico. Do ponto de vista técnico, define as classes que o quadro de injeção de dependência deve utilizar na construção da aplicação. Na maioria dos casos, usa Dash.di.DashContext.

Em seguida, instantiate a classe primária do quadro dash.js, MediaPlayer. Esta classe contém os métodos fundamentais necessários, como reproduzir e fazer pausas, gere a relação com o elemento de vídeo e gere também a interpretação do ficheiro Media Presentation Description (MPD), que descreve o vídeo a ser reproduzido.

A função de arranque da classe MediaPlayer é chamada para garantir que o jogador está pronto para reproduzir o vídeo. Entre outras coisas, a função garante que todas as classes necessárias (conforme definido pelo contexto) foram carregadas. Uma vez que o leitor esteja pronto, pode ligar o elemento de vídeo a ele utilizando a função attachView(). A função de arranque permite ao MediaPlayer injetar o fluxo de vídeo no elemento e também controlar a reprodução conforme necessário.

Passe o URL do ficheiro MPD para o MediaPlayer para que saiba sobre o vídeo que se espera que seja reproduzido. A função configuraçãoVideo() acabada de ser executada terá de ser executada uma vez que a página esteja totalmente carregada. Faça-o utilizando o evento de carga do elemento do corpo. Mude `<body>` o seu elemento para:

```html
    <body onload="setupVideo()">
```

Por fim, deset o tamanho do elemento de vídeo utilizando CSS. Num ambiente de streaming adaptativo, isto é especialmente importante porque o tamanho do vídeo que está a ser reproduzido pode mudar à medida que a reprodução se adapta às condições de rede em mudança. Nesta simples demonstração simplesmente força o elemento de vídeo a ser 80% da janela disponível do navegador adicionando o seguinte CSS à secção principal da página:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Jogando um vídeo
Para reproduzir um vídeo, aponte o seu navegador para o ficheiro basicPlayback.html e clique em reproduzir no leitor de vídeo apresentado.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também

[Repositório GitHub dash.js](https://github.com/Dash-Industry-Forum/dash.js) 

