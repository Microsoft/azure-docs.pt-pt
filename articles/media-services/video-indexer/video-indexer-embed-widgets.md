---
title: Widgets do Indexer de Vídeo incorporados nas suas apps
titleSuffix: Azure Media Services
description: Saiba como incorporar widgets do Video Indexer nas suas apps.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 1bc751ae293abbb7aa330a99a4b66a917d150906
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994983"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Widgets do Indexer de Vídeo incorporados nas suas apps

Este artigo mostra como pode incorporar widgets do Video Indexer nas suas aplicações. O Video Indexer suporta a incorporação de três tipos de widgets nas aplicações: *Cognitive Insights*, *Player* e *Editor*.

Começando pela versão 2, o URL base widget inclui a região da conta especificada. Por exemplo, uma conta na região oeste dos EUA gera: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Um widget Cognitive Insights inclui todas as informações visuais que foram extraídas do processo de indexação do seu vídeo. O widget Cognitive Insights suporta os seguintes parâmetros de URL opcionais:

|Nome|Definição|Descrição|
|---|---|---|
|`widgets` | Cadeias separadas por vírgulas | Permite-lhe controlar as ideias que pretende dar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` torna apenas as pessoas e palavras-chave uI insights.<br/>Opções disponíveis: pessoas, animaçõesCharacters,palavras-chave, rótulos, sentimentos, emoções, tópicos, quadros-chave, transcrição, ocr, altifalantes, cenas e nomes.|
|`controls`|Cadeias separadas por vírgulas|Permite-lhe controlar os controlos que pretende renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` torna apenas a opção de pesquisa e o botão de descarregamento.<br/>Opções disponíveis: pesquisa, download, predefinição, idioma.|
|`language`|Um código de linguagem curta (nome da língua)|Controla a linguagem dos insights.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ou `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Um código de linguagem curta | Controla a linguagem da UI. O valor predefinido é `en`. <br/>Exemplo: `locale=de`.|
|`tab` | O separador selecionado por defeito | Controla o **separador Insights** que é renderizado por defeito. <br/>Exemplo: `tab=timeline` torna as informações com o **separador Timeline** selecionado.|
|`location` ||O `location` parâmetro deve ser incluído nos links incorporados, ver como obter o nome da sua [região.](regions.md) Se a sua conta estiver em pré-visualização, `trial` deve ser utilizada para o valor de localização. `trial` é o valor padrão para o `location` parâmetro.| 

### <a name="player-widget"></a>Widget Player

Pode utilizar o widget do Jogador para transmitir o vídeo utilizando uma taxa de bits adaptativa. O widget player suporta os seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`t` | Segundos desde o início | Faz com que o jogador comece a jogar a partir do ponto de tempo especificado.<br/> Exemplo: `t=60`. |
|`captions` | Um código de linguagem | Procura a legenda no idioma especificado durante o carregamento de widgets para estar disponível no menu **Legendas.**<br/> Exemplo: `captions=en-US`. |
|`showCaptions` | Um valor booleano | Faz com que o leitor carregue já com as legendas ativadas.<br/> Exemplo: `showCaptions=true`. |
|`type`| | Ativa uma pele do leitor de áudio (a parte de vídeo é removida).<br/> Exemplo: `type=audio`. |
|`autoplay` | Um valor booleano | Indica se o leitor deve começar a reproduzir o vídeo quando carregado. O valor predefinido é `true`.<br/> Exemplo: `autoplay=false`. |
|`language`/`locale` | Um código de linguagem | Controla a linguagem do jogador. O valor predefinido é `en-US`.<br/>Exemplo: `language=de-DE`.|
|`location` ||O `location` parâmetro deve ser incluído nos links incorporados, ver como obter o nome da sua [região.](regions.md) Se a sua conta estiver em pré-visualização, `trial` deve ser utilizada para o valor de localização. `trial` é o valor padrão para o `location` parâmetro.| 

### <a name="editor-widget"></a>Widget editor

Você pode usar o widget editor para criar novos projetos e gerir as insights de um vídeo. O widget editor suporta os seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Fornece acesso a vídeos que estão apenas na conta que é usada para incorporar o widget.<br> O widget editor requer o `accessToken` parâmetro. |
|`language` | Um código de linguagem | Controla a linguagem do jogador. O valor predefinido é `en-US`.<br/>Exemplo: `language=de-DE`. |
|`locale` | Um código de linguagem curta | Controla a linguagem dos insights. O valor predefinido é `en`.<br/>Exemplo: `language=de`. |
|`location` ||O `location` parâmetro deve ser incluído nos links incorporados, ver como obter o nome da sua [região.](regions.md) Se a sua conta estiver em pré-visualização, `trial` deve ser utilizada para o valor de localização. `trial` é o valor padrão para o `location` parâmetro.| 

<sup>*</sup>O proprietário deve ter `accessToken` cuidado.

## <a name="embedding-videos"></a>Incorporação de vídeos

Esta secção discute a incorporação de conteúdo público e privado em apps.

O `location` parâmetro deve ser incluído nos links incorporados, ver como obter o nome da sua [região.](regions.md) Se a sua conta estiver em pré-visualização, `trial` deve ser utilizada para o valor de localização. `trial` é o valor padrão para o `location` parâmetro. Por exemplo: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> A partilha de um link para o widget **Player** ou **Insights** incluirá o token de acesso e concederá as permissões apenas de leitura na sua conta.

### <a name="public-content"></a>Conteúdo público

1. Inscreva-se no site do [Video Indexer.](https://www.videoindexer.ai/)
1. Selecione o vídeo com o quais pretende trabalhar e prima **Play**.
1. Selecione o tipo de widget que deseja **(Cognitive Insights,** **Player**, ou **Editor).**
1. Clique **&lt; / &gt; em Incorporar**.
5. Copie o código incorporado (aparece na **Cópia do código incorporado** no diálogo Share & **Embed).**
6. Adicione o código à sua aplicação.

### <a name="private-content"></a>Conteúdo privado

Para incorporar um vídeo privado, tem de passar um token de acesso no `src` atributo do iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Para obter o conteúdo widget Do Cognitive Insights, utilize um dos seguintes métodos:

- The [Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API.<br/>
- O [Token de Acesso de Vídeo Get](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adicione-o como um parâmetro de consulta ao URL. Especifique este URL como o `src` valor do iframe, como mostrado anteriormente.

Para fornecer capacidades de edição de insights no seu widget incorporado, tem de passar um token de acesso que inclua permissões de edição. Use [Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou [Obter Token de acesso a vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) com `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interação de widgets

O widget Cognitive Insights pode interagir com um vídeo na sua aplicação. Esta secção mostra-lhe como alcançar esta interação.

![Índice de vídeo widget de insights cognitivos](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Visão geral do fluxo

Quando edita as transcrições, ocorre o seguinte fluxo:

1. Edita a transcrição na linha do tempo.
1. O Video Indexer recebe estas atualizações e guarda-as nas [edições de transcrição](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) no modelo de idioma.
1. As legendas são atualizadas:

    * Se estiver a utilizar o widget do leitor do Video Indexer - é automaticamente atualizado.
    * Se estiver a utilizar um leitor externo - obtém um novo utilizador de ficheiros de legendas, a chamada **de legendas de vídeo.**

### <a name="cross-origin-communications"></a>Comunicações com várias origens

Para obter widgets do Video Indexer para comunicar com outros componentes, o serviço De Indexer de Vídeo:

- Utiliza o método de comunicação de origem cruzada HTML5 `postMessage` .
- Valida a mensagem na origem VideoIndexer.ai.

Se implementar o seu próprio código de jogador e integrar-se com widgets Cognitive Insights, é da sua responsabilidade validar a origem da mensagem que vem de VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorporar widgets na sua app ou blog (recomendado)

Esta secção mostra como obter interação entre dois widgets do Video Indexer para que quando um utilizador selecione o controlo de insights na sua aplicação, o jogador salta para o momento relevante.

1. Copie o código de incorporação do widget Player.
2. Copie o código de incorporação do widget Cognitive Insights.
3. Adicione o [ficheiro Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para processar a comunicação entre os dois widgets:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um utilizador seleciona o controlo de insights na sua aplicação, o jogador salta para o momento relevante.

Para obter mais informações, consulte o [Índice de Vídeo - Incorpore ambas as demonstrações widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporar o widget Cognitive Insights e utilizar o Leitor de Multimédia do Azure para reproduzir os conteúdos

Esta secção mostra como obter a interação entre um widget Cognitive Insights e um exemplo de Azure Media Player utilizando o [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Adicione um plug-in do Indexer de vídeo para o leitor AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instantiate Azure Media Player com o plug-in do Indexer de Vídeo.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Copie o código de incorporação do widget Cognitive Insights.

Agora pode comunicar com o Azure Media Player.

Para obter mais informações, consulte a [demo do Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporar o widget de Video Indexer Cognitive Insights e usar um leitor de vídeo diferente

Se utilizar um leitor de vídeo que não seja o Azure Media Player, tem de manipular manualmente o leitor de vídeo para conseguir a comunicação.

1. Insira o seu leitor de vídeo.

    Por exemplo, um leitor HTML5 padrão:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Incorpore o widget Cognitive Insights.
3. Implemente a comunicação no seu leitor ao escutar o evento message (mensagem). Por exemplo:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Para obter mais informações, consulte a [demo do Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionar legendas

Se incorporar insights do Video Indexer com o seu próprio [Azure Media Player,](https://aka.ms/azuremediaplayer)pode utilizar o `GetVttUrl` método para obter legendas fechadas (legendas). Também pode chamar um método JavaScript a partir do plug-in AMP do Indexer de Vídeo `getSubtitlesUrl` (como mostrado anteriormente).

## <a name="customizing-embeddable-widgets"></a>Personalizar widgets incorporáveis

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Pode escolher os tipos de insights que quiser. Para isso, especifique-os como um valor para o seguinte parâmetro URL que é adicionado ao código incorporado que obtém (a partir da API ou da aplicação web): `&widgets=<list of wanted widgets>` .

Os valores possíveis são: `people` , , , , , , , , `animatedCharacters` , , , `keywords` , , , `labels` e `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Por exemplo, se quiser incorporar um widget que contenha apenas informações de pessoas e palavras-chave, o URL incorporado no iframe será assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

O título da janela iframe também pode ser personalizado fornecendo `&title=<YourTitle>` ao URL iframe. (Personaliza o `<title>` valor HTML).
   
Por exemplo, se quiser dar à sua janela iframe o título "MyInsights", o URL será assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Tenha em atenção que esta opção só é relevante caso precise de abrir as informações numa nova janela.

### <a name="player-widget"></a>Widget Player

Se incorporar o leitor do Video Indexer, pode selecionar o tamanho do leitor ao especificar o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por predefinição, o leitor de Indexer de Vídeo tem legendas fechadas autogeradas que se baseiam na transcrição do vídeo. A transcrição é extraída do vídeo com a língua de origem que foi selecionada quando o vídeo foi carregado.

Se quiser incorporar uma linguagem diferente, pode adicionar `&captions=<Language Code>` ao URL do jogador incorporado. Se quiser que as legendas sejam exibidas por defeito, pode passar &showCaptions=verdadeiro.

O URL incorporado será então assim:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Reprodução automática

Por predefinição, o jogador começará a reproduzir o vídeo. pode optar por não passar para `&autoplay=false` o URL incorporado anterior.

## <a name="code-samples"></a>Exemplos de código

Consulte o [repo de amostras](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) de código que contém amostras para API e Widgets indexantes de vídeo:

| Arquivo/pasta                       | Descrição                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Carregue o vídeo do indexante de vídeo num Azure Media Player personalizado.                        |
| `azure-media-player-vi-insights`  | Incorporar VI Insights com um Azure Media Player personalizado.                             |
| `control-vi-embedded-player`      | Incorporar o jogador VI e controlá-lo de fora.                                    |
| `custom-index-location`           | Incorporar VI Insights a partir de uma localização externa personalizada (pode ser cliente uma bolha).     |
| `embed-both-insights`             | Utilização básica do VI Insights tanto do jogador como dos insights.                            |
| `embed-insights-with-AMP`         | Inserir VI Insights widget com um Azure Media Player personalizado.                      |
| `customize-the-widgets`           | Incorpore vi widgets com opções personalizadas.                                     |
| `embed-both-widgets`              | Incorporar VI Player e Insights e comunicar entre eles.                      |
| `url-generator`                   | Gera widgets personalizados incorporados URL com base em opções especificadas pelo utilizador.             |
| `html5-player`                    | Incorporar VI Insights com um leitor de vídeo HTML5 padrão.                           |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como visualizar e editar insights do Indexer de Vídeo, consulte [visualizar e editar insights do Indexer de Vídeo](video-indexer-view-edit.md).

Além disso, confira [o indexante de vídeo CodePen.](https://codepen.io/videoindexer/pen/eGxebZ)
