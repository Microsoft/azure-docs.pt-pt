---
title: Inserir Video Indexer widgets em seus aplicativos
titlesuffix: Azure Media Services
description: Saiba como incorporar widgets do Video Indexer na sua aplicação.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ec3c7379c8c7f28765fbc4396d3e9804a6c127f6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663752"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>Inserir Video Indexer widgets em seus aplicativos

Este artigo mostra-lhe como pode incorporar widgets do Video Indexer nas suas aplicações. O Video Indexer dá suporte à inserção de três tipos de widgets em seu aplicativo: **Informações cognitivas**, **Player**e **Editor**. 

A partir da versão 2, a URL base do widget inclui a região da conta especificada. Por exemplo, uma conta na região oeste dos EUA gera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Um widget **Cognitive Insights** inclui todas as informações visuais que foram extraídas do processo de indexação do seu vídeo. O widget de informações suporta os seguintes parâmetros de URL:

|Nome|Definição|Descrição|
|---|---|---|
|`widgets`|Cadeias separadas por vírgulas|Permite-lhe controlar as informações que pretende compor. <br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` irá compor apenas as informações de IU de marcas e pessoas<br/>Opções disponíveis: people (pessoas), keywords (palavras-chave), annotations (anotações), brands (marcas), sentiments (sentimentos), transcript (transcrição), search (pesquisa).<br/>Não suportado se o URL tiver o parâmetro version=2.<br/><br/>**Nota:** Não há suporte para o parâmetro de URL de widgets na versão 2. |
|`locale`|Um código de idioma curto|Controla o idioma do insights. A predefinição é `en`. Por exemplo: `language=de`.|
|`tab`|A guia selecionada padrão|Controla a guia insights que é renderizada por padrão. `tab=timeline`Renderiza as informações com a guia linha do tempo selecionada.|

### <a name="player-widget"></a>Widget Player

Um widget **Player** permite-lhe transmitir o vídeo com uma velocidade de transmissão adaptável. O widget Player suporta os seguintes parâmetros de URL:

|Nome|Definição|Descrição|
|---|---|---|
|`t`|Segundos desde o início|Faz com que o leitor inicie a reprodução a partir de um determinado momento.<br/>Exemplo: `t=60`.|
|`captions`|Um código de idioma|Obtém a legenda no idioma especificado durante o carregamento do widget para ficar disponível no menu das legendas.<br/>Exemplo: `captions=en-US`.|
|`showCaptions`|Um valor booliano|Faz com que o leitor carregue já com as legendas ativadas.<br/>Exemplo: `showCaptions=true`.|
|`type`||Ativa uma máscara de leitor de áudio (a peça de vídeo é removida).<br/>Exemplo: `type=audio`.|
|`autoplay`|Um valor booliano|Indica se o leitor deve iniciar a reprodução do vídeo ao ser carregado (predefinição: true)<br/>Exemplo: `autoplay=false`.|
|`language`|Um código de idioma|Controla o idioma do leitor (predefinição: en-US)<br/>Exemplo: `language=de-DE`.|

### <a name="editor-widget"></a>Widget do editor 

O widget **Editor** permite que você crie novos projetos e gerencie informações do vídeo.

|Nome|Definição|Descrição|
|---|---|---|
|`accessToken`<sup>*</sup>|Cadeia|O `accessToken` parâmetro é necessário ao usar o widget do editor.<br/>O token de acesso fornece acesso a vídeos que estão apenas dentro da conta usada para inserir o widget. |
|`language`|Um código de idioma|Controla o idioma do leitor (predefinição: en-US)<br/>Exemplo: `language=de-DE`.|
|`locale`|Um código de idioma curto|Controla o idioma do insights. A predefinição é `en`. Por exemplo: `language=de`.|

<sup>*</sup>O proprietário deve ser fornecido `accessToken` com cuidado. 

## <a name="embedding-public-content"></a>Incorporar conteúdos públicos

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
2. Clique no vídeo com o qual pretende trabalhar.
3. Clique no botão Embed (Incorporar) abaixo do vídeo.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Após clicar no botão, será apresentada uma caixa de diálogo de incorporação no ecrã, onde poderá selecionar o widget que pretende incorporar na sua aplicação.
    A seleção de um widget (**ideias cognitivas**, **Player**ou **Editor**) gera o código inserido para você colar em seu aplicativo.
 
4. Escolha o tipo de widget desejado (**ideias cognitivas**, **Player**ou **Editor**).
5. Copie o código de incorporação e adicione-o à sua aplicação. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> Se você tiver problemas com o compartilhamento de suas URLs de vídeo, tente adicionar o parâmetro ' location ' ao link. O parâmetro deve ser definido para as [regiões do Azure nas quais Video indexer existe](regions.md). Por exemplo, `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Incorporar conteúdos privados

Só é possível obter códigos de incorporação a partir de janelas de pop-up (conforme mostrado na secção anterior) para vídeos **Públicos**. 

Se quiser incorporar um vídeo **Privado**, terá de passar um token de acesso no atributo **src** do **iframe**:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Utilize a API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) para obter os conteúdos do widget Cognitive Insights ou a API [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) e adicione-a ao URL como um parâmetro de consulta, conforme mostrado acima. Especifique este URL como o valor **src** do **iframe**.

Se quiser fornecer funcionalidades de informações de edição (como fizemos na nossa aplicação Web) ao seu widget incorporado, terá de passar um token de acesso com permissões de edição. Utilize [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) com **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interação de widgets

O widget **Cognitive Insights** pode interagir com um vídeo na sua aplicação. Esta secção mostra-lhe como alcançar esta interação.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações com várias origens

Para fazer com que os widgets do Video Indexer comuniquem com outros componentes, o serviço Video Indexer faz o seguinte:

- Utiliza o método HTML5 de comunicação com várias origens **postMessage** 
- Valida a mensagem na origem VideoIndexer.ai. 

Se optar por implementar o seu próprio código de leitor e fazer a integração com os widgets **Cognitive Insights**, é responsável por validar a origem da mensagem proveniente de VideoIndexer.ai.

### <a name="embed-widgets-in-your-application--blog-recommended"></a>Inserir widgets em seu aplicativo/blog (recomendado) 

Esta secção mostra-lhe como alcançar a interação entre os dois widgets do Video Indexer para que quando um utilizador clicar no controlo de informações na sua aplicação, o leitor passe para o momento relevante.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

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

Se você inserir informações de Video Indexer com seu próprio [player de mídia do Azure](https://aka.ms/azuremediaplayer), poderá usar o método **GetVttUrl** para obter legendas ocultas (legendas). Também pode chamar um método JavaScript a partir do plug-in AMP do Video Indexer **getSubtitlesUrl** (conforme mostrado anteriormente). 

## <a name="customizing-embeddable-widgets"></a>Personalizar widgets incorporáveis

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Você pode escolher os tipos de informações que deseja especificando-os como um valor para o seguinte parâmetro de URL adicionado ao código de inserção obtido (da API ou do aplicativo Web): `&widgets=<list of wanted widgets>`.

Os valores possíveis são: people (pessoas), keywords (palavras-chave), sentiments (sentimentos), transcript (transcrição), search (pesquisa).

Por exemplo, se você quiser inserir um widget contendo apenas pessoas e informações de pesquisa, a URL de inserção do iframe terá a seguinte aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

O título da janela do iframe também pode ser personalizado fornecendo `&title=<YourTitle>` à URL do iframe. (irá personalizar o valor \<title> de html).
    
Por exemplo, se você quiser dar à janela iframe o título "mysights", a URL terá a seguinte aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Tenha em atenção que esta opção só é relevante caso precise de abrir as informações numa nova janela.

### <a name="player-widget"></a>Widget Player

Se incorporar o leitor do Video Indexer, pode selecionar o tamanho do leitor ao especificar o tamanho do iframe.

Por exemplo:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por predefinição, o leitor do Video Indexer terá legendas geradas automaticamente com base na transcrição do vídeo extraída do vídeo com o idioma de origem selecionado ao carregar o vídeo.

Se você quiser inserir um idioma diferente, poderá adicionar `&captions=< Language | ”all” | “false” >` à URL do player de inserção ou colocar "todos" como o valor se desejar ter todas as legendas de idiomas disponíveis.
Se desejar que as legendas sejam exibidas por padrão, você poderá passar `&showCaptions=true`.

A URL de inserção terá a seguinte aparência: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se quiser desativar as legendas, pode passar o valor "false" para o parâmetro captions (legendas).

Reprodução automática: por predefinição, o leitor iniciará a reprodução do vídeo. Pode optar por desativar esta opção ao passar o valor &autoplay=false para o URL de incorporação acima.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como ver e editar informações do Video Indexer, veja [este](video-indexer-view-edit.md) artigo.

Além disso, confira CodePen do indexador de [vídeo](https://codepen.io/videoindexer/pen/eGxebZ).
