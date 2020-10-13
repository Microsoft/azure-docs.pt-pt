---
title: Plugin de streaming suave para o quadro de mídia open source
description: Saiba como utilizar o plugin de streaming suave dos serviços Azure Media para o Adobe Open Source Media Framework.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b71a2064887cc2b6099ead2e59f9fa3113d2f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269017"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Como utilizar o Plugin de streaming suave do Microsoft para o Adobe Open Source Media Framework

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Descrição geral
O plugin de streaming da Microsoft Smooth para Open Source Media Framework 2.0 (SS for OSMF) alarga as capacidades padrão do OSMF e adiciona a reprodução de conteúdo sonoro do Microsoft Smooth streaming a novos e existentes jogadores OSMF. O plugin também adiciona capacidades de reprodução de streaming suave à Reprodução de Mídia Strobe (SMP).

SS para OSMF inclui duas versões de plugin:

* Plugin de streaming estático liso para OSMF (.swc)
* Plugin de streaming suave dinâmico para OSMF (.swf)

Este documento pressupõe que o leitor tem um conhecimento geral de funcionamento dos plug-ins DAMF e OSMF. Para mais informações sobre a OSMF, consulte a documentação no site oficial da [OSMF.](http://osmf.org/)

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Plugin de streaming liso para OSMF 2.0
O plugin suporta o carregamento e reprodução de conteúdos de Streaming Smooth a pedido com as seguintes funcionalidades:

* Reprodução de streaming suave a pedido (Reproduzir, Pausa, Procurar, Parar)
* Reprodução de Streaming Suave ao Vivo (Play)
* Funções de DVR ao vivo (Pausa, Procura, Reprodução de DVR, Go-to-Live)
* Suporte para códigos de vídeo - H.264
* Suporte para codecs áudio - AAC
* Comutação múltipla de linguagem áudio com APIs incorporados os OSMF
* Seleção de qualidade de reprodução máxima com APIs incorporados os OSMF
* Sidecar fechou legendas com legendas OSMF plugin
* Adobe &reg; Flash &reg; Player 11.4 ou superior.
* Esta versão suporta apenas o OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Funcionalidades suportadas e questões conhecidas
Para obter uma lista completa de funcionalidades suportadas, funcionalidades não apoiadas e questões conhecidas, consulte [este documento](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Carregar o Plugin
Os plugins OSMF podem ser carregados estáticamente (no tempo de compilação) ou dinamicamente (no tempo de execução). O plugin Smooth Streaming para download do OSMF inclui versões dinâmicas e estáticas.

* Carga estática: Para carregar estáticamente, é necessário um ficheiro de biblioteca estática (SWC). Os plugins estáticos são adicionados como referência aos projetos e fundem-se dentro do ficheiro de saída final na hora da compilação.
* Carregamento dinâmico: Para carregar dinamicamente, é necessário um ficheiro pré-compensado (SWF). Os plugins dinâmicos são carregados no tempo de execução e não incluídos na saída do projeto. (Saída compilada) Os plugins dinâmicos podem ser carregados utilizando protocolos HTTP e FILE.

Para obter mais informações sobre o carregamento estático e dinâmico, consulte a página oficial do [plugin DAMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS para carregamento estático osmf
O corte de código abaixo mostra como carregar o plugin SS para OSMF estáticamente e reproduzir um vídeo básico usando a classe MEDIAFactory OSMF. Antes de incluir o código SS para OSMF, certifique-se de que a referência do projeto inclui o plugin estático "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```csharp
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


### <a name="ss-for-osmf-dynamic-loading"></a>SS para carregamento dinâmico osmmF
O corte de código abaixo mostra como carregar o plugin SS para OSMF dinamicamente e reproduzir um vídeo básico utilizando a classe MEDIAFactory OSMF. Antes de incluir o plugin dinâmico SS para OSMF, copie o plugin dinâmico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" na pasta do projeto se pretender carregar usando o protocolo FILE ou copiar num servidor web para carga HTTP. Não há necessidade de incluir "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nas referências do projeto.

```csharp
package 
{

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
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Reprodução de Mídia Strobe com o Plugin Dinâmico SS ODMF
O streaming suave para plugin dinâmico OSMF é compatível com [a Reprodução de Mídia Strobe (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). Pode utilizar o plugin SS para plugin OSMF para adicionar a reprodução de conteúdo de Streaming Suave à SMP. Para isso, copie "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" num servidor web para carregamento HTTP utilizando os seguintes passos:

1. Navegue na página de configuração da [reprodução de mídia Strobe](http://osmf.org/dev/2.0gm/setup.html). 
2. Defina o src numa fonte de streaming suave (por exemplo, http: \/ /devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Faça as alterações de configuração desejadas e clique em Pré-visualização e Atualização.
   
   **Nota** O seu servidor web de conteúdo precisa de uma crossdomain.xml válida. 
4. Copie e cole o código para uma página HTML simples utilizando o seu editor de texto favorito, como no exemplo seguinte:

    ```html
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
    ```


1. Adicione plugin OSMF de streaming suave ao código incorporado e guarde.
   
    ```html
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
    ```

2. Guarde a sua página HTML e publique num servidor web. Navegue na página web publicada utilizando o seu navegador de &reg; Internet favorito (Internet Explorer, Chrome, Firefox, assim por diante).
3. Desfrute de conteúdos de streaming suave dentro do Adobe &reg; Flash &reg; Player.

Para obter mais informações sobre o desenvolvimento geral da OSMF, consulte a página oficial de desenvolvimento da [OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Microsoft Adaptive Streaming Plugin para atualização OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

