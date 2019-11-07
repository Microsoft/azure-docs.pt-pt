---
title: Introdução ao Azure Media Clipper | Microsoft Docs
description: Introdução ao Azure Media Clipper, uma ferramenta para a criação de clipes de vídeo de ativos do AMS
services: media-services
keywords: clip; subclip; codificação; mídia
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685071"
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar clipes com o Azure Media Clipper
Esta seção mostra as etapas básicas de introdução ao Azure Media Clipper. As seções a seguir fornecem as informações específicas sobre como configurar o Azure Media Clipper.

- Primeiro, adicione os links a seguir para Player de Mídia do Azure e Azure Media Clipper para o cabeçalho do documento. É recomendável especificar explicitamente uma versão do Clipper e Player de Mídia do Azure nas URLs. Não use a versão mais recente desses recursos em produção, pois eles estão sujeitos a alterações sob demanda.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Em seguida, adicione as seguintes classes ao elemento div em que você gostaria de instanciar o Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para habilitar o tema escuro, adicione a classe de capa escura:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Em seguida, instancie o Clipper com a seguinte chamada à API:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Os parâmetros para a chamada do método de inicialização são:
- `selector` {REQUIRED, String}: seletor CSS do elemento HTML correspondente em que o widget deve ser renderizado.
- `restVersion` {REQUIRED, String}: a versão do API REST de Serviços de Mídia do Azure para o destino. A versão REST define o formato da saída gerada pelo widget. Atualmente, há suporte apenas para 2,0.
- `submitSubclipCallback` {REQUIRED, Promise} a função de retorno de chamada invocada quando o botão "enviar" do widget é clicado. A função de retorno de chamada deve esperar a saída gerada pelo widget (uma configuração de trabalho de renderização ou uma definição de filtro). Para obter mais informações, consulte enviar retorno de chamada de subclipe.
- `logLevel` {OPTIONAL, {' info ', ' WARN ', ' erro '}}: o nível de log a ser exibido no console do navegador. Valor padrão: erro
- `minimumMarkerGap` {OPTIONAL, int}: o tamanho mínimo de um subclipe (em segundos). Observação: o valor deve ser maior ou igual a 6, que também é o padrão.
- `singleBitrateMp4Profile` {OPTIONAL, JSON Object} o perfil MP4 de taxa de bits única a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se não for fornecido, ele usará o [perfil MP4 padrão de taxa de bits única](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPTIONAL, JSON Object} o perfil MP4 de múltiplas taxas de bits a ser usado para a configuração do trabalho de renderização gerada pelo widget. Se não for fornecido, ele usará o [perfil MP4 padrão de múltiplas taxas de bits](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPTIONAL, JSON Object} permite personalizar os atalhos de teclado do widget. Para obter mais informações, consulte [atalhos de teclado personalizáveis](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {OPTIONAL, Promise} a função de retorno de chamada invocada para carregar (de forma assíncrona) uma nova página de ativos no painel ativos toda vez que o usuário rola para baixo até a parte inferior do painel. Para obter mais informações, consulte retorno de chamada do carregador do painel do ativo.
- `height` {OPTIONAL, Number} a altura total do widget (a altura mínima é 600 PX sem o painel de ativos e 850 PX com o painel ativos).
- `subclippingMode` (opcional, {' all', ' render ', ' filtro '}): os modos de subcorte permitidos. O valor padrão é ALL.
- `filterAssetsTypes` (opcional, bool): filterAssetsTypes permite mostrar/ocultar o menu suspenso filtros no painel ativos. O valor padrão é true.
- `speedLevels` (opcional, matriz): speedLevels permite definir níveis de velocidade diferentes para o player de vídeo, consulte a [documentação player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) para obter mais informações.
- `resetOnJobDone` (opcional, bool): resetOnJobDone permite que o Clipper redefina o subclipe para um estado inicial quando um trabalho é enviado com êxito.
- `autoplayVideo` (opcional, bool): autoplayVideo permite que o Clipper reproduzido o vídeo ao carregar. O valor padrão é true.
- `language` {OPTIONAL, Cadeia de caracteres}: Language define o idioma do widget. Se não for especificado, o widget tentará localizar as mensagens com base no idioma do navegador. Se nenhum idioma for detectado no navegador, o widget assume o padrão de inglês. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md) .
- `languages` {OPTIONAL, JSON}: o parâmetro Languages substitui o dicionário padrão de idiomas por um dicionário personalizado definido pelo usuário. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md) .
- `extraLanguages` (opcional, JSON): o parâmetro extraLanguages adiciona novos idiomas ao dicionário padrão. Para obter mais informações, consulte a seção [Configurar localização](media-services-azure-media-clipper-localization.md) .

## <a name="typescript-definition"></a>Definição do TypeScript
Um arquivo de definição do [TypeScript](https://www.typescriptlang.org/) para o Clipper pode ser encontrado [aqui](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API do Azure Media Clipper
Esta seção documenta a superfície de API fornecida pelo Clipper.

- `ready(handler)`: o oferece uma maneira de executar o JavaScript assim que o Clipper é totalmente carregado e pronto para ser usado.
- `load(assets)`: carrega uma lista de ativos na linha do tempo do widget (não deve ser usada junto com assetsPanelLoaderCallback). Consulte este [artigo](media-services-azure-media-clipper-load-assets.md) para obter detalhes sobre como carregar ativos no Clipper.
- `setLogLevel(level)`: define o nível de log a ser exibido no console do navegador. Os valores possíveis são: `info`, `warn`, `error`.
- `setHeight(height)`: define a altura total do widget em pixels (a altura mínima é 600 PX sem o painel de ativos e 850 PX com o painel ativos).
- `version`: Obtém a versão do widget.

## <a name="next-steps"></a>Passos seguintes
Consulte as próximas etapas para configurar o Azure Media Clipper:
- [Carregando ativos no Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configurando atalhos de teclado personalizados](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Enviando trabalhos de recorte do Clipper](media-services-azure-media-clipper-submit-job.md)
- [Configurando a localização](media-services-azure-media-clipper-localization.md)
