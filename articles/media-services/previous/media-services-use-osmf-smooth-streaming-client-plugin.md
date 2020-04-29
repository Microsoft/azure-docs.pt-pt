---
title: Plugin de streaming suave para o quadro de mídia de código aberto
description: Saiba como utilizar o plugin de streaming de streaming liso dos Serviços De Comunicação Social Azure para o Quadro de Meios de Comunicação Aberto adobe.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 71d28a19316bf1b618ec9008a5e96a503687b202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681952"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como utilizar o Plugin de Streaming Suave da Microsoft para a Estrutura de Meios de Comunicação De Código Aberto da Adobe  
## <a name="overview"></a>Descrição geral
O plugin de streaming do Microsoft Smooth para Open Source Media Framework 2.0 (SS for OSMF) alarga as capacidades padrão da OSMF e adiciona a reprodução de conteúdo do Microsoft Smooth Streaming a novos e já existentes jogadores DESMF. O plugin também adiciona capacidades de reprodução de Smooth Streaming à Strobe Media Playback (SMP).

SS para OSMF inclui duas versões de plugin:

* Plugin de streaming liso estático para OSMF (.swc)
* Plugin de streaming suave dinâmico para OSMF (.swf)

Este documento pressupõe que o leitor tem um conhecimento geral de trabalho dos plug-ins OSMF e OSMF. Para mais informações sobre a OSMF, consulte a documentação no site oficial da [OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Plugin de streaming suave para OSMF 2.0
O plugin suporta a carga e a reprodução de conteúdos smooth streaming a pedido com as seguintes funcionalidades:

* Reprodução de Streaming Suave a pedido (Reproduzir, Pausa, Procurar, Parar)
* Reprodução de Live Smooth Streaming (Reprodução)
* Funções de DVR ao vivo (Pausa, Procura, DVR Playback, Go-to-Live)
* Suporte para codificadores de vídeo - H.264
* Suporte para codificadores áudio - AAC
* Troca de linguagem áudio múltipla com APIs incorporados da OSMF
* Seleção de qualidade de reprodução max com APIs incorporados da OSMF
* Sidecar fechou legendas com legendas OSMF plugin
* Adobe&reg; &reg; Flash Player 11.4 ou superior.
* Esta versão suporta apenas os OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Características suportadas e questões conhecidas
Para obter uma lista completa de funcionalidades suportadas, funcionalidades não suportadas e questões conhecidas, consulte [este documento.](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)

## <a name="loading-the-plugin"></a>Carregar o Plugin
Os plugins OSMF podem ser carregados estáticamente (no tempo de compilação) ou dinamicamente (no tempo de execução). O plugin Smooth Streaming para download de OSMF inclui versões dinâmicas e estáticas.

* Carga estática: Para carregar estática, é necessário um ficheiro de biblioteca estática (SWC). Os plugins estáticos são adicionados como referência aos projetos e fundem-se dentro do ficheiro de saída final no momento da compilação.
* Carga dinâmica: Para carregar dinamicamente, é necessário um ficheiro pré-compilado (SWF). Os plugins dinâmicos são carregados no tempo de execução e não incluídos na saída do projeto. (Produção compilada) Os plugins dinâmicos podem ser carregados utilizando protocolos HTTP e FILE.

Para obter mais informações sobre o carregamento estático e dinâmico, consulte a página oficial [de plugin sMF .](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)

### <a name="ss-for-osmf-static-loading"></a>SS para carregamento estático OSMF
O código abaixo mostra como carregar o plugin SS para OSMF estáticamente e reproduzir um vídeo básico usando a classe OSMF MediaFactory. Antes de incluir o código SS para o OSMF, certifique-se de que a referência do projeto inclui o plugin estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico osmf
O código abaixo mostra como carregar o plugin SS para OSMF dinamicamente e reproduzir um vídeo básico usando a classe OSMF MediaFactory. Antes de incluir o ss para o código OSMF, copie o "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" da pasta do projeto se pretender carregar usando o protocolo FILE, ou copiar sob um servidor web para a carga HTTP. Não há necessidade de incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências do projeto.

pacote {

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reprodução de Mídia Strobe com o Plugin Dinâmico SS ODMF
O Smooth Streaming para plugin dinâmico OSMF é compatível com a Reprodução de [Strobe Media (SMP)](http://osmf.org/strobe_mediaplayback.html). Pode utilizar o Plugin SS para o OSMF para adicionar a reprodução de conteúdo de Streaming Suave ao SMP. Para tal, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" num servidor web para a carga HTTP utilizando os seguintes passos:

1. Navegue na página de [configuração da Reprodução Strobe Media](http://osmf.org/dev/2.0gm/setup.html). 
2. Desloque o src para uma fonte de\/streaming suave (por exemplo, http: /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Faça as alterações de configuração desejadas e clique em Pré-visualização e Atualização.
   
   **Nota** O seu servidor web de conteúdo necessita de um crossdomain.xml válido. 
4. Copie e cole o código numa página HTML simples utilizando o seu editor de texto favorito, como no seguinte exemplo:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Adicione o plugin OSMF de streaming suave ao código incorporado e guarde.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Guarde a sua página HTML e publique num servidor web. Navegue na página web publicada&reg; utilizando o seu Flash Player favorito ativado o navegador de Internet (Internet Explorer, Chrome, Firefox, assim por diante).
3. Desfrute de conteúdos de&reg; &reg; Streaming Suave dentro do Adobe Flash Player.

Para obter mais informações sobre o desenvolvimento geral da OSMF, consulte a página oficial de [desenvolvimento da OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Plugin de streaming adaptativo da Microsoft para atualização do OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

