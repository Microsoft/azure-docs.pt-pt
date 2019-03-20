---
title: Incorporar widgets do indexador de vídeos nas suas aplicações
titlesuffix: Azure Media Services
description: Saiba como incorporar widgets do Video Indexer na sua aplicação.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: fd680b00feb8a75dfec952d7211554100fca00d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075071"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Incorporar widgets do indexador de vídeos nas suas aplicações

Este artigo mostra-lhe como pode incorporar widgets do Video Indexer nas suas aplicações. O Video Indexer suporta incorporação dois tipos de widgets na sua aplicação: **Insights cognitivos** e **Player**. 

> [!NOTE]
> A partir de 1 de Fevereiro de 2018, versão 1 dos **Insights cognitivos** widget vai ser preterido. A versão de URL de incorporação será predefinido para `version=2`.

A partir da versão 2, o URL de base do widget inclui a região da conta. Por exemplo, gera uma conta na região Oeste dos E.U.A.: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Um widget **Cognitive Insights** inclui todas as informações visuais que foram extraídas do processo de indexação do seu vídeo. O widget de informações suporta os seguintes parâmetros de URL:

|Name|Definição|Descrição|
|---|---|---|
|widgets|Cadeias separadas por vírgulas|Permite-lhe controlar as informações que pretende compor. <br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` irá compor apenas as informações de IU de marcas e pessoas<br/>Opções disponíveis: people (pessoas), keywords (palavras-chave), annotations (anotações), brands (marcas), sentiments (sentimentos), transcript (transcrição), search (pesquisa).<br/>Não suportado se o URL tiver o parâmetro version=2.<br/><br/>**Nota:** O **widgets** param de URL não é suportada se **versão = 2** é utilizado. |
|versão|Versões do widget **Cognitive Insights**|Para obter as informações mais recentes atualizações de widget, adicionar `?version=2` param para o URL de incorporação de consulta. Por exemplo, `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2` <br/> Para obter a versão mais antiga, remova o parâmetro `version=2` do URL.

### <a name="player-widget"></a>Widget Player

Um widget **Player** permite-lhe transmitir o vídeo com uma velocidade de transmissão adaptável. O widget Player suporta os seguintes parâmetros de URL:

|Name|Definição|Descrição|
|---|---|---|
|t|Segundos a partir do início|Faz com que o leitor inicie a reprodução a partir de um determinado momento.<br/>Exemplo: t=60|
|captions|Código do idioma|Obtém a legenda no idioma especificado durante o carregamento do widget para ficar disponível no menu das legendas.<br/>Exemplo: captions=pt-PT|
|showCaptions|Um valor booleano|Faz com que o leitor carregue já com as legendas ativadas.<br/>Exemplo: showCaptions=true|
|tipo||Ativa uma máscara de leitor de áudio (a peça de vídeo é removida).<br/>Exemplo: type=audio|
|autoplay|Um valor booleano|Indica se o leitor deve iniciar a reprodução do vídeo ao ser carregado (predefinição: true)<br/>Exemplo: autoplay=false|
|language|Código do idioma|Controla o idioma do leitor (predefinição: en-US)<br/>Exemplo: language=pt-PT|

## <a name="embedding-public-content"></a>Incorporar conteúdos públicos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Clique no vídeo com o qual pretende trabalhar.
3. Clique no botão Embed (Incorporar) abaixo do vídeo.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Após clicar no botão, será apresentada uma caixa de diálogo de incorporação no ecrã, onde poderá selecionar o widget que pretende incorporar na sua aplicação.
    Selecionar um widget (**Player** ou **Cognitive Insights**) gera o código de incorporação para colar na sua aplicação.
 
4. Selecione o tipo de widget que pretende (**Cognitive Insights** ou **Player**).
5. Copie o código de incorporação e adicione-o à sua aplicação. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Incorporar conteúdos privados

Só é possível obter códigos de incorporação a partir de janelas de pop-up (conforme mostrado na secção anterior) para vídeos **Públicos**. 

Se quiser incorporar um vídeo **Privado**, terá de passar um token de acesso no atributo **src** do **iframe**:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Utilize a API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) para obter os conteúdos do widget Cognitive Insights ou a API [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) e adicione-a ao URL como um parâmetro de consulta, conforme mostrado acima. Especifique este URL como o valor **src** do **iframe**.

Se quiser fornecer funcionalidades de informações de edição (como fizemos na nossa aplicação Web) ao seu widget incorporado, terá de passar um token de acesso com permissões de edição. Utilize [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) ou [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) com **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interação de widgets

O widget **Cognitive Insights** pode interagir com um vídeo na sua aplicação. Esta secção mostra-lhe como alcançar esta interação.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações com várias origens

Para fazer com que os widgets do Video Indexer comuniquem com outros componentes, o serviço Video Indexer faz o seguinte:

- Utiliza o método HTML5 de comunicação com várias origens **postMessage** 
- Valida a mensagem na origem VideoIndexer.ai. 

Se optar por implementar o seu próprio código de leitor e fazer a integração com os widgets **Cognitive Insights**, é responsável por validar a origem da mensagem proveniente de VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Incorporar os dois tipos de widgets na sua aplicação/blogue (recomendado) 

Esta secção mostra-lhe como alcançar a interação entre os dois widgets do Video Indexer para que quando um utilizador clicar no controlo de informações na sua aplicação, o leitor passe para o momento relevante.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> `

1. Copie o código de incorporação do widget **Player**.
2. Copie o código de incorporação do widget **Cognitive Insights**.
3. Adicione o [**ficheiro Mediator**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para processar a comunicação entre os dois widgets:

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora quando um utilizador clicar no controlo de informações na sua aplicação, o leitor irá passar para o momento relevante.

Para obter mais informações, veja [esta demonstração](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporar o widget Cognitive Insights e utilizar o Leitor de Multimédia do Azure para reproduzir os conteúdos

Esta secção mostra-lhe como alcançar a interação entre um widget **Cognitive Insights** e uma instância do Leitor de Multimédia do Azure através do [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Adicione um plug-in do Video Indexer ao leitor AMP.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instancie o Leitor de Multimédia do Azure com o plug-in do Video Indexer.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
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

        // Init your AMP instance
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
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Copie o código de incorporação do widget **Cognitive Insights**.

Agora deverá conseguir comunicar com o seu Leitor de Multimédia do Azure.

Para obter mais informações, veja [esta demonstração](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Incorporar o widget Cognitive Insights do Video Indexer e utilizar o seu próprio leitor (pode utilizar qualquer leitor)

Se utilizar o seu leitor, terá de manipulá-lo para efetuar a comunicação. 

1. Insira o seu leitor de vídeo.

    Por exemplo, um leitor HTML5 padrão.

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorpore o widget Cognitive Insights.
3. Implemente a comunicação no seu leitor ao escutar o evento message (mensagem). Por exemplo:

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Para obter mais informações, veja [esta demonstração](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionar legendas

Se incorporar informações do Video Indexer com o seu leitor AMP, pode utilizar o método **GetVttUrl** para obter legendas. Também pode chamar um método JavaScript a partir do plug-in AMP do Video Indexer **getSubtitlesUrl** (conforme mostrado anteriormente). 

## <a name="customizing-embeddable-widgets"></a>Personalizar widgets incorporáveis

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Pode escolher os tipos de informações que pretende, especificando-as como um valor para o parâmetro de URL seguinte adicionado para o código de incorporação que obtenha (a partir da API ou da aplicação web): `&widgets=<list of wanted widgets>`.

Os valores possíveis são: people (pessoas), keywords (palavras-chave), sentiments (sentimentos), transcript (transcrição), search (pesquisa).

Por exemplo, se quiser incorporar um widget que contém apenas informações de pessoas e pesquisa de URL de incorporação de iframe ficará assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

O título da janela do iframe também pode ser personalizado, fornecendo `&title=<YourTitle>` para o URL do iframe. (irá personalizar o valor \<title> de html).
    
Por exemplo, se quiser fornecer sua janela de iframe o título "MyInsights", o URL ficará assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Tenha em atenção que esta opção só é relevante caso precise de abrir as informações numa nova janela.

### <a name="player-widget"></a>Widget Player

Se incorporar o leitor do Video Indexer, pode selecionar o tamanho do leitor ao especificar o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por predefinição, o leitor do Video Indexer terá legendas geradas automaticamente com base na transcrição do vídeo extraída do vídeo com o idioma de origem selecionado ao carregar o vídeo.

Se deseja incorporar com um idioma diferente, pode adicionar `&captions=< Language | ”all” | “false” >` para o URL de leitor de incorporação ou put "todos" como o valor, se quiser ter todas as legendas de idiomas disponíveis.
Se pretender que as legendas para ser apresentado por predefinição, pode passar `&showCaptions=true`.

O URL de incorporação, em seguida, terá o seguinte aspeto: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se quiser desativar as legendas, pode passar o valor "false" para o parâmetro captions (legendas).

Reprodução automática: por predefinição, o leitor iniciará a reprodução do vídeo. Pode optar por desativar esta opção ao passar o valor &autoplay=false para o URL de incorporação acima.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como ver e editar informações do Video Indexer, veja [este](video-indexer-view-edit.md) artigo.

Veja também o [CodePen do Video Indexer](https://codepen.io/videoindexer/pen/eGxebZ).
