---
title: Incorporar um vídeo de transmissão em fluxo Adaptivo MPEG-DASH numa aplicação HTML5 com dash. js | Documentos da Microsoft
description: Este tópico demonstra como incorporar um vídeo de transmissão em fluxo Adaptivo MPEG-DASH numa aplicação HTML5 com dash. js.
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
ms.openlocfilehash: 1390d68a93dd3d80b5977bab6665553ea3f8b398
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403079"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporar um vídeo de transmissão em fluxo Adaptivo MPEG-DASH numa aplicação HTML5 com dash. js  

## <a name="overview"></a>Descrição geral
MPEG-DASH é um padrão ISO para a transmissão em fluxo adaptativa de conteúdo de vídeo, que oferece benefícios significativos para os desenvolvedores que desejam disponibilizar transmissões de saída de vídeos de alta qualidade, adaptável. Com MPEG-DASH, o fluxo de vídeo se ajusta automaticamente a uma definição inferior quando a rede fique congestionada. Isso reduz a probabilidade do Visualizador a ver um vídeo "em pausa", enquanto o player baixa os seguintes alguns segundos para reproduzir (também conhecido como colocação em memória intermédia). Como reduz o congestionamento de rede, o leitor de vídeo por sua vez será retornado para um fluxo de maior qualidade. Essa capacidade de adaptar-se a largura de banda necessária também resulta numa hora de início mais rápida para vídeo. Isso significa que o primeiro alguns segundos podem ser reproduzidos num segmento de qualidade mais baixo do fast para download e, em seguida, migrar para um conteúdo de uma vez suficientes de qualidade superior tem sido colocados em memória intermédia.

Dash. js é um código-fonte aberto MPEG-DASH player de vídeo escrito no JavaScript. Sua meta é fornecer um jogador robusto e para várias plataformas que pode ser reutilizado livremente em aplicações que necessitam de reprodução de vídeo. Ele fornece reprodução de MPEG-DASH em qualquer browser que suporte o W3C suporte de dados de origem extensões (MSE), hoje em dia Chrome, Microsoft Edge e IE11 (outros browsers indicado sua intenção para suportar o MSE). Para obter mais informações sobre dash. js, js, veja o repositório do GitHub dash. js.

## <a name="creating-a-browser-based-streaming-video-player"></a>Criando um player de vídeo transmissão em fluxo baseada no browser
Para criar uma página da web simples que exibe um player de vídeo com a esperada controla tais um play, pause, o recuo etc., terá de:

1. Criar uma página HTML
2. Adicione a etiqueta de vídeo
3. Adicionar o jogador dash. js
4. Inicializar o leitor
5. Adicionar alguns estilos CSS
6. Ver os resultados num navegador que implementa o MSE

A inicializar o jogador pode ser concluída em apenas algumas poucas linhas de código JavaScript. Utilizar dash. js, realmente é simples incorporar o vídeo de MPEG-DASH em seus aplicativos baseados em navegador.

## <a name="creating-the-html-page"></a>Criar a página HTML
A primeira etapa é criar um padrão HTML página que contém o **vídeo** ilustra do elemento, guarde este ficheiro como basicPlayer.html, como o exemplo seguinte:

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

## <a name="adding-the-dashjs-player"></a>Adicionar o jogador dash. js
Para adicionar a implementação de referência do dash. js para a aplicação, terá de obter o arquivo dash.all.js da versão mais recente do projeto dash. js. Isso deverá ser salvo na pasta JavaScript do seu aplicativo. Este ficheiro é um arquivo de conveniência que reúne todos os códigos de dash. js necessários num único arquivo. Se tiver uma olhada em todo o repositório de dash. js, encontra os arquivos individuais, testar o código e muito mais, mas se tudo o que deseja fazer é utilizar dash. js, em seguida, o ficheiro de dash.all.js é o que precisa.

Para adicionar o jogador dash. js às suas aplicações, adicione uma marca de script para a seção de cabeçalho basicPlayer.html:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Em seguida, crie uma função para inicializar o jogador quando a página for carregada. Adicione o seguinte script após a linha em que carregar dash.all.js:

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

Essa função cria primeiro um DashContext. Isto é utilizado para configurar a aplicação para um ambiente de runtime específica. Do ponto de vista técnico, ele define as classes que a estrutura de injeção de dependência deve utilizar ao construir o aplicativo. Na maioria dos casos, utilize Dash.di.DashContext.

Em seguida, crie uma instância da classe principal do framework dash. js, MediaPlayer. Essa classe contém o núcleo de métodos necessários, tais como reproduzirem e colocar em pausa, gere a relação com o elemento de vídeo e também gerencia a interpretação do arquivo de descrição de apresentação de suporte de dados (MPD), que descreve o vídeo a reprodução.

A função de startup() da classe MediaPlayer denomina-se para se certificar de que o jogador está pronto para reproduzir um vídeo. Entre outras coisas, a função garante que todas as classes necessárias (conforme definido pelo contexto) tem sido carregadas. Assim que o player estiver pronto, pode anexar o elemento de vídeo a ele usando a função de attachView(). A função de inicialização permite que o MediaPlayer injetar o fluxo de vídeo no elemento e também controlar a reprodução conforme necessário.

Passe a URL do ficheiro MPD para o MediaPlayer, para que ele sabe sobre o vídeo que é esperado para reproduzir. A função de setupVideo() acabou de criar tem de ser executado assim que a página estiver completamente carregado. Fazer com o evento onload do elemento de corpo. Alterar sua <body> elemento para:

```html
    <body onload="setupVideo()">
```

Por fim, defina o tamanho do elemento de vídeo usando CSS. Num ambiente de transmissão em fluxo adaptável, isso é especialmente importante porque o tamanho do vídeo a ser reproduzido podem ser alterados conforme a reprodução se adapta às mudanças nas condições de rede. Nesta demonstração simples força simplesmente o elemento de vídeo para ser 80% da janela do navegador disponíveis ao adicionar o CSS seguinte à seção de cabeçalho da página:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Reproduzir um vídeo
Para reproduzir um vídeo, apontar o seu browser para o ficheiro de basicPlayback.html e clique em reproduzir no player de vídeo apresentado.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

[Repositório do GitHub dash. js](https://github.com/Dash-Industry-Forum/dash.js) 

