---
title: Widgets de Indexer de Vídeo incorporados nas suas apps
titleSuffix: Azure Media Services
description: Aprenda a incorporar widgets de Indexer de Vídeo nas suas apps.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 6ed5c509cf310b743e4ef52f411dfa34e5db09c1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411617"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Widgets de Indexer de Vídeo incorporados nas suas apps

Este artigo mostra como pode incorporar widgets de Indexer de Vídeo nas suas apps. O Indexer de vídeo suporta incorporar três tipos de widgets nas suas apps: *Cognitive Insights,* *Player*, e *Editor*.

Começando pela versão 2, o URL base de widget inclui a região da conta especificada. Por exemplo, uma conta na região `https://wus2.videoindexer.ai/embed/insights/...`oeste dos EUA gera: .

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Um widget Cognitive Insights inclui todas as informações visuais que foram extraídas do processo de indexação do seu vídeo. O widget Cognitive Insights suporta os seguintes parâmetros de URL opcionais:

|Nome|Definição|Descrição|
|---|---|---|
|`widgets` | Cadeias separadas por vírgulas | Permite-lhe controlar as ideias que pretende render.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` torna apenas pessoas e palavras-chave uI insights.<br/>Opções disponíveis: pessoas, personagens animados ,palavras-chave, rótulos, sentimentos, emoções, tópicos, quadros-chave, transcrição, ocr, altifalantes, cenas e nomes Entidades.|
|`controls`|Cadeias separadas por vírgulas|Permite-lhe controlar os controlos que pretende renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` torna apenas a opção de pesquisa e o botão de descarregamento.<br/>Opções disponíveis: pesquisa, download, predefinições, idioma.|
|`language`|Um código de linguagem curto (nome de língua)|Controla a linguagem insights.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ou`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Um código de linguagem curto | Controla a linguagem da UI. O valor predefinido é `en`. <br/>Exemplo: `locale=de`.|
|`tab` | O separador selecionado por defeito | Controla o separador **Insights** que é renderizado por defeito. <br/>Exemplo: `tab=timeline` torna as informações com o **separador Timeline** selecionado.|

### <a name="player-widget"></a>Widget Player

Pode utilizar o widget Player para transmitir vídeo utilizando uma taxa de bit adaptável. O widget Player suporta os seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`t` | Segundos desde o início | Faz com que o jogador comece a jogar a partir do ponto de tempo especificado.<br/> Exemplo: `t=60`. |
|`captions` | Um código de linguagem | A legenda no idioma especificado durante o carregamento de widget saca para estar disponível no menu **Legendas.**<br/> Exemplo: `captions=en-US`. |
|`showCaptions` | Um valor booleano | Faz com que o leitor carregue já com as legendas ativadas.<br/> Exemplo: `showCaptions=true`. |
|`type`| | Ativa uma pele de leitor de áudio (a parte de vídeo é removida).<br/> Exemplo: `type=audio`. |
|`autoplay` | Um valor booleano | Indica se o leitor deve começar a reproduzir o vídeo quando carregado. O valor predefinido é `true`.<br/> Exemplo: `autoplay=false`. |
|`language`/`locale` | Um código de linguagem | Controla a linguagem dos jogadores. O valor predefinido é `en-US`.<br/>Exemplo: `language=de-DE`.|

### <a name="editor-widget"></a>Widget editor

Você pode usar o widget Editor para criar novos projetos e gerir as ideias de um vídeo. O widget editor suporta os seguintes parâmetros de URL opcionais.

|Nome|Definição|Descrição|
|---|---|---|
|`accessToken`<sup>*</sup> | Cadeia | Fornece acesso a vídeos que estão apenas na conta que é usado para incorporar o widget.<br> O widget editor `accessToken` requer o parâmetro. |
|`language` | Um código de linguagem | Controla a linguagem dos jogadores. O valor predefinido é `en-US`.<br/>Exemplo: `language=de-DE`. |
|`locale` | Um código de linguagem curto | Controla a linguagem insights. O valor predefinido é `en`.<br/>Exemplo: `language=de`. |

<sup>*</sup>O proprietário `accessToken` deve providenciar cautela.

## <a name="embedding-public-content"></a>Incorporar conteúdos públicos

1. Inscreva-se no site do Indexer de [Vídeo.](https://www.videoindexer.ai/)
2. Selecione o vídeo com o que pretende trabalhar.
3. Selecione o**</>** botão incorporado () que aparece sob o vídeo.

    Depois de selecionar o botão **Embed,** pode selecionar o widget que pretende incorporar na sua aplicação.
4. Selecione o tipo de widget que deseja (**Cognitive Insights,** **Player,** ou **Editor).**
5. Copiar o código incorporado (aparece em **Copiar o código incorporado** no diálogo Share & **Incorporado).**
6. Adicione o código à sua aplicação.

> [!NOTE]
> Se tiver problemas em partilhar os seus `location` URLs de vídeo, adicione o parâmetro ao link. O parâmetro deve ser definido para as [regiões de Azure em que existe](regions.md)o Indexer de Vídeo. Por exemplo: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Incorporar conteúdos privados

Para incorporar um vídeo privado, deve passar um `src` sinal de acesso no atributo do iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Para obter o conteúdo do widget Cognitive Insights, utilize um dos seguintes métodos:

- A [API Get Insights Widget.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- The [Get Video Access Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Adicione-o como parâmetro de consulta ao URL. Especifique `src` este URL como o valor para o iframe, como mostrado anteriormente.

Para fornecer capacidades de edição de insights no seu widget incorporado, você deve passar um sinal de acesso que inclui permissões de edição. Use [Obter Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou obter `&allowEdit=true`vídeo access [token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) com .

## <a name="widgets-interaction"></a>Interação de widgets

O widget Cognitive Insights pode interagir com um vídeo na sua aplicação. Esta secção mostra-lhe como alcançar esta interação.

![Índice de vídeo de widget de insights cognitivos](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações com várias origens

Para obter widgets de Indexer de Vídeo para comunicar com outros componentes, o serviço Indexer de Vídeo:

- Utiliza o método `postMessage`de comunicação de origem cruzada HTML5 .
- Valida a mensagem na origem VideoIndexer.ai.

Se implementar o seu próprio código de jogador e integrar-se com widgets Cognitive Insights, é da sua responsabilidade validar a origem da mensagem que vem de VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Widgets incorporados na sua app ou blog (recomendado)

Esta secção mostra como alcançar a interação entre dois widgets do Indexer de Vídeo para que, quando um utilizador selecione o controlo de insights na sua aplicação, o jogador salta para o momento relevante.

1. Copie o código de incorporação do widget Player.
2. Copie o código de incorporação do widget Cognitive Insights.
3. Adicione o [ficheiro Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para processar a comunicação entre os dois widgets:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um utilizador seleciona o controlo de insights na sua aplicação, o jogador salta para o momento relevante.

Para mais informações, consulte o Indexer de [Vídeo - Embed ambos os Widgets demo](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporar o widget Cognitive Insights e utilizar o Leitor de Multimédia do Azure para reproduzir os conteúdos

Esta secção mostra como alcançar a interação entre um widget Cognitive Insights e uma instância azure Media Player utilizando o [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Adicione um plug-in do Indexer de Vídeo para o leitor AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instantiate Azure Media Player com o plug-in do Indexer de Vídeo.

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

3. Copie o código de incorporação do widget Cognitive Insights.

Agora pode comunicar com o Azure Media Player.

Para mais informações, consulte a [demonstração azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/rYONrO)

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporar o widget de Insights Cognitivos do Indexer de Vídeo e usar um leitor de vídeo diferente

Se utilizar um leitor de vídeo que não o Azure Media Player, deve manipular manualmente o leitor de vídeo para obter a comunicação.

1. Insira o seu leitor de vídeo.

    Por exemplo, um leitor html5 padrão:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorpore o widget Cognitive Insights.
3. Implemente a comunicação no seu leitor ao escutar o evento message (mensagem). Por exemplo:

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

Para mais informações, consulte a [demonstração azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/YEyPLd)

## <a name="adding-subtitles"></a>Adicionar legendas

Se incorporar informações do Indexer de Vídeo com o `GetVttUrl` seu próprio [Azure Media Player,](https://aka.ms/azuremediaplayer)pode utilizar o método para obter legendas fechadas (legendas). Também pode ligar para um método JavaScript a `getSubtitlesUrl` partir do plug-in do Indexante de Vídeo AMP (como mostrado anteriormente).

## <a name="customizing-embeddable-widgets"></a>Personalizar widgets incorporáveis

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Pode escolher os tipos de insights que quiser. Para isso, especifique-os como um valor para o seguinte parâmetro URL que é adicionado ao código `&widgets=<list of wanted widgets>`incorporado que obtém (da API ou da aplicação web): .

Os valores `people`possíveis `keywords` `labels`são: `emotions` `topics`, `keyframes` `transcript` `ocr` `speakers` `animatedCharacters` `sentiments`, , `scenes`, `namedEntities`, , , , e .

Por exemplo, se quiser incorporar um widget que contenha apenas pessoas e palavras-chave, o URL incorporado para iframe será assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

O título da janela do iframe também `&title=<YourTitle>` pode ser personalizado fornecendo ao URL iframe. (Personaliza o `<title>` valor HTML).
   
Por exemplo, se quiser dar à sua janela de iframe o título "MyInsights", o URL será assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Tenha em atenção que esta opção só é relevante caso precise de abrir as informações numa nova janela.

### <a name="player-widget"></a>Widget Player

Se incorporar o leitor do Video Indexer, pode selecionar o tamanho do leitor ao especificar o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por padrão, o leitor de Indexer de Vídeo tem legendas fechadas autogeradas que são baseadas na transcrição do vídeo. A transcrição é extraída do vídeo com a linguagem de origem que foi selecionada quando o vídeo foi carregado.

Se quiser incorporar com uma linguagem diferente, `&captions=<Language Code>` pode adicionar ao URL do leitor incorporado. Se pretender que as legendas sejam exibidas por padrão, pode passar &showCaptions=verdade.

O URL incorporado será então assim:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Reprodução automática

Por padrão, o leitor começará a reproduzir o vídeo. pode optar por não `&autoplay=false` o fazer passando para o URL incorporado anterior.

## <a name="code-samples"></a>Exemplos de código

Consulte o repo de [amostras](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) de código que contém amostras para a API e Widgets do Indexer de Vídeo:

| Arquivo/pasta                       | Descrição                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Carregue o vídeo do indexador de vídeo num Azure Media Player personalizado.                        |
| `azure-media-player-vi-insights`  | Incorporar VI Insights com um Azure Media Player personalizado.                             |
| `control-vi-embedded-player`      | Incorporar o Jogador VI e controlá-lo de fora.                                    |
| `custom-index-location`           | Incorporar vis Insights de uma localização externa personalizada (pode ser cliente uma bolha).     |
| `embed-both-insights`             | Uso básico de VI Insights tanto jogador como insights.                            |
| `embed-insights-with-AMP`         | Inbed VI Insights widget com um Azure Media Player personalizado.                      |
| `customize-the-widgets`           | Inbed VI widgets com opções personalizadas.                                     |
| `embed-both-widgets`              | Incorporar o Jogador VI e insights e comunicar entre eles.                      |
| `url-generator`                   | Gera widgets personalizados incorporados URL com base em opções especificadas pelo utilizador.             |
| `html5-player`                    | Incorporar VIS Insights com um leitor de vídeo HTML5 predefinido.                           |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como visualizar e editar insights do Indexer de Vídeo, consulte [ver e editar insights do Indexer](video-indexer-view-edit.md)de Vídeo .

Além disso, confira o indexante de [vídeo CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
