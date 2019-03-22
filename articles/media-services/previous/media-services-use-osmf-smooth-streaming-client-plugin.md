---
title: Plugin de transmissão em fluxo uniforme para a estrutura de suporte de dados de código-fonte aberto
description: Saiba como utilizar o plug-in Azure Media Services Smooth Streaming para o Adobe Open Source Media Framework.
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
ms.openlocfilehash: c40d8b93a7487619cc94586c7e6b4cdc550435cd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314064"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como utilizar o Smooth Streaming Plug-in para o Adobe Open Source Media Framework da Microsoft  
## <a name="overview"></a>Descrição geral
O plug-in Microsoft Smooth Streaming para abrir origem Media Framework 2.0 (SS para OSMF) expande as capacidades de padrão de OSMF e adiciona a reprodução de conteúdo Microsoft Smooth Streaming para jogadores OSMF novos e existentes. O plug-in também adiciona capacidades de transmissão em fluxo uniforme de reprodução para Strobe Media Playback (SMP).

SS para OSMF inclui duas versões do plug-in:

* Estático plugin Smooth Streaming para OSMF (.swc)
* Dinâmica plugin Smooth Streaming para OSMF (.swf)

Este documento assume que o leitor tenha um conhecimento prático do OSMF e OSMF plug-ins. Para obter mais informações sobre OSMF, consulte a documentação sobre o [site oficial do OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Plug-in de Streaming para OSMF 2.0
O plug-in suporta carregamento e a reprodução de conteúdo de transmissão em fluxo uniforme de demanda com as seguintes funcionalidades:

* Reprodução de transmissão em fluxo uniforme de sob demanda (Play, Pause, Seek, Stop)
* Reprodução de transmissão em fluxo uniforme em direto (reproduzir)
* Funções DVR em direto (colocar em pausa, Seek, reprodução de DVR, Go-to-Live)
* Suporte para codecs de vídeo - H.264
* Suporte para codecs de áudio - AAC
* Vários idiomas de áudio mudar com APIs de internas OSMF
* Seleção de qualidade de reprodução de Max com APIs de internas OSMF
* Sidecar legendas Plug-in de legendas de áudio do OSMF
* Adobe&reg; Flash&reg; Player 11.4 ou superior.
* Esta versão suporta apenas OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funcionalidades suportadas e problemas conhecidos
Para obter uma lista completa das funcionalidades suportadas, funcionalidades não suportadas e problemas conhecidos, consulte [este documento](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).

## <a name="loading-the-plugin"></a>A carregar o plug-in
Plug-ins OSMF podem ser carregados estaticamente (no momento da compilação) ou dinamicamente (em tempo de execução). O plug-in Smooth Streaming para OSMF download inclui versões estáticas e dinâmicas.

* Carregamento estático: Para carregar estaticamente, é necessário um ficheiro de biblioteca estática (SWC). Plug-ins estáticos são adicionados como uma referência a projetos e intercalação dentro do arquivo de saída final em tempo de compilação.
* Carregamento dinâmico: Para carregar dinamicamente, é necessário um ficheiro de (SWF) pré-compilados. Plug-ins dinâmicos são carregados no tempo de execução e não incluídos na saída do projeto. (Saída compilada) Plug-ins dinâmicos podem ser carregados usando protocolos HTTP e o ficheiro.

Para obter mais informações sobre carregamento estática e dinâmica, consulte oficial [página de plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS para carregamento de estático OSMF
O trecho de código abaixo mostra como carregar o plug-in de SS para OSMF estaticamente e reproduzir um vídeo básico usando a classe de OSMF MediaFactory. Antes de incluir o SS para o código OSMF, certifique-se que a referência de projeto inclui o plug-in de estático de "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

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


### <a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico de OSMF
O trecho de código abaixo mostra como carregar o plug-in de SS para OSMF dinamicamente e reproduzir básica vídeo usando a classe OSMF MediaFactory. Antes de incluir o SS para o código OSMF, copie o plug-in de dinâmico de "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" para a pasta de projeto, se pretender carregar através do protocolo de ficheiros ou copie num servidor web para a carga HTTP. Não é necessário incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências do projeto.

{de pacote

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reprodução de mídia strobe com o plug-in de dinâmica de SS ODMF
Smooth Streaming para OSMF Plug-in do dinâmica é compatível com [Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html). Pode usar o SS Plug-in de OSMF para adicionar a reprodução de conteúdo de transmissão em fluxo uniforme ao SMP. Para tal, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" num servidor web para a carga HTTP utilizando os seguintes passos:

1. Procurar os [página de configuração de reprodução de mídia Strobe](http://osmf.org/dev/2.0gm/setup.html). 
2. Definir o src a uma origem de transmissão em fluxo uniforme, (por exemplo http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Faça as alterações de configuração pretendida e clique em pré-visualização e atualização.
   
   **Tenha em atenção** seu servidor de conteúdo web tem um crossdomain. XML válido. 
4. Copie e cole o código para uma página HTML simples usando o seu editor de texto favorito, tal como no exemplo a seguir:

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



1. Adicione o plug-in de Smooth Streaming para OSMF para o código de incorporação e guarde.
   
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
2. Guardar a sua página HTML e publicar a um servidor web. Navegue para a página da web publicados usando o Flash favorito&reg; Player habilitado browser da Internet (Internet Explorer, Chrome, Firefox, etc.).
3. Desfrute de conteúdos de transmissão em fluxo uniforme de mensagens em fila no Adobe&reg; Flash&reg; Player.

Para obter mais informações sobre o desenvolvimento de OSMF geral, veja o artigo oficial [página de desenvolvimento OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Microsoft adaptável de transmissão em fluxo Plug-in de atualização de OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

