---
title: Introdução ao Azure Media Clipper | Documentos da Microsoft
description: Introdução ao Azure Media Clipper, uma ferramenta para a criação de clips de vídeo dos recursos do AMS
services: media-services
keywords: Clip; subclip; codificação; suporte de dados
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51848b9ba4d18b3ac7d652cfbd97cab6b85f2ee8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842367"
---
# <a name="create-clips-with-azure-media-clipper"></a>Crie clips a com o Azure Media Clipper
Esta secção mostra-lhe os passos básicos de introdução ao Azure Media Clipper. Secções que se seguem fornecem detalhes sobre como configurar o Azure Media Clipper.

- Primeiro, adicione as seguintes ligações para o leitor de multimédia do Azure e Azure Media Clipper a frente do seu documento. É recomendável especificar explicitamente uma versão do leitor de multimédia do Azure e Clipper nos URLs. Não utilize a versão mais recente destes recursos na produção como estão sujeitas a alterações a pedido.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Em seguida, adicione as seguintes classes para o elemento div onde pretende criar uma instância do Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionalmente, para habilitar o tema no escuro, adicione a classe de capa escuro:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Em seguida, criar uma instância Clipper com a seguinte chamada à API:

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

Os parâmetros para a chamada de método de inicialização são:
- `selector` {REQUIRED, string}: Seletor CSS do elemento HTML correspondente em que deve ser composto o widget.
- `restVersion` {REQUIRED, string}: A versão de API de REST de serviços de multimédia do Azure para o destino. A versão REST define o formato da saída gerada pelo widget. Atualmente, apenas 2.0 é suportado.
- `submitSubclipCallback` {Promessa necessária} A função de retorno de chamada invocada quando se clica no botão "Enviar" do widget. A função de retorno de chamada deve esperar que o resultado gerado pelo widget (uma configuração de trabalho de composição ou uma definição de filtro). Para obter mais informações, consulte o retorno de chamada do envio subclip.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: O nível de registo a ser apresentado na consola do browser. Valor predefinido: erro
- `minimumMarkerGap` {OPTIONAL, int}: O tamanho mínimo de um subclip (em segundos). Nota: o valor deve ser maior ou igual a 6, que também é a predefinição.
- `singleBitrateMp4Profile` {OPCIONAL, objeto JSON} O perfil de mp4 de velocidade de transmissão única para utilizar para a configuração de trabalho de composição gerada pelo widget. Se não for indicado, ele usa o [predefinido o perfil de MP4 de velocidade de transmissão única](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {OPCIONAL, objeto JSON} O perfil de mp4 de velocidade de transmissão múltipla a utilizar para processar a configuração de trabalho gerada pelo widget. Se não for indicado, ele usa o [predefinido o perfil de MP4 de velocidade de transmissão](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {OPCIONAL, objeto de json} Permite personalizar os atalhos de teclado do widget. Para obter mais informações, consulte [atalhos de teclado personalizável](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {Promessa opcional} A função de retorno de chamada invocada para carregar (de forma assíncrona) uma nova página de recursos para o painel de recursos sempre que o utilizador desloca para baixo para a parte inferior do painel. Para obter mais informações, consulte o retorno de chamada do recurso painel carregador.
- `height` {OPCIONAL, number} A altura total do widget (altura mínima é de 600 px sem o painel de recursos e 850 px com o painel de recursos).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): O mode(s) subdistorção permitido. O valor predefinido é tudo.
- `filterAssetsTypes` (Opcional, bool): filterAssetsTypes permitem-lhe mostrar/ocultar o menu pendente de filtros do painel de recursos. O valor predefinido é verdadeiro.
- `speedLevels` (Opcional, matriz): speedLevels permite definir níveis diferentes de velocidade para o player de vídeo, consulte [documentação de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) para obter mais informações.
- `resetOnJobDone` (Opcional, bool): resetOnJobDone permite Clipper para repor o subclipper para um Estado inicial quando uma tarefa é submetida com êxito.
- `autoplayVideo` (Opcional, bool): autoplayVideo permite Clipper reprodução automática do vídeo na carga. O valor predefinido é verdadeiro.
- `language` {Cadeia de caracteres opcional,}: linguagem define o idioma do widget. Se não for especificado, o widget tenta localizar as mensagens com base no idioma do navegador. Se nenhuma linguagem for detetada no navegador, o widget é predefinido para inglês. Para obter mais informações, consulte a [configurar a localização](media-services-azure-media-clipper-localization.md) secção.
- `languages` {OPCIONAL, JSON}: o parâmetro de idiomas substitui o dicionário predefinido das linguagens com um dicionário personalizado definido pelo utilizador. Para obter mais informações, consulte a [configurar a localização](media-services-azure-media-clipper-localization.md) secção.
- `extraLanguages` (Opcional, JSON): o parâmetro extraLanguages adiciona novos idiomas ao dicionário predefinido. Para obter mais informações, consulte a [configurar a localização](media-services-azure-media-clipper-localization.md) secção.

## <a name="typescript-definition"></a>Definição do typeScript
R [TypeScript](https://www.typescriptlang.org/) pode ser encontrado o ficheiro de definição para o Clipper [aqui](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API do Azure Media Clipper
Esta secção documenta a superfície de API fornecida pelo Clipper.

- `ready(handler)`: oferece uma forma de executar o JavaScript assim que o Clipper estiver totalmente carregada e pronta para ser utilizada.
- `load(assets)`: carrega uma lista de recursos para a linha cronológica de widget (não deve ser utilizado em conjunto com assetsPanelLoaderCallback). Ver isso [artigo](media-services-azure-media-clipper-load-assets.md) para obter detalhes sobre como carregar recursos para o Clipper.
- `setLogLevel(level)`: define o nível de registo a ser apresentado na consola do browser. Os valores possíveis são: `info`, `warn`, `error`.
- `setHeight(height)`: define a altura total do widget em pixéis (altura mínima é de 600 px sem o painel de recursos e 850 px com o painel de recursos).
- `version`: obtém a versão de widget.

## <a name="next-steps"></a>Passos Seguintes
Veja os passos seguintes para configurar o Azure Media Clipper:
- [Carregamento de recursos no Azure Media Clipper](media-services-azure-media-clipper-load-assets.md)
- [Configurar atalhos de teclado personalizados](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Submeter tarefas de recorte do Clipper](media-services-azure-media-clipper-submit-job.md)
- [Configurar localização](media-services-azure-media-clipper-localization.md)