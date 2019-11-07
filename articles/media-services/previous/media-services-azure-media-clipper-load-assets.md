---
title: Carregar ativos no Azure Media Clipper | Microsoft Docs
description: Etapas para carregar ativos no Azure Media Clipper
services: media-services
keywords: clip; subclip; codificação; mídia
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 66b4ca5b2859dd306f6eb1c669a07840189f53d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685052"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Carregando ativos no Azure Media Clipper  

Os ativos podem ser carregados no Azure Media Clipper por dois métodos:
1. Passagem estática em uma biblioteca de ativos
2. Gerando dinamicamente uma lista de ativos por meio da API

## <a name="statically-load-videos-into-clipper"></a>Carregar estaticamente vídeos no Clipper
Carregue estaticamente vídeos no Clipper para permitir que os usuários finais criem clipes sem selecionar vídeos no painel de seleção de ativos.

Nesse caso, você passa um conjunto estático de ativos para o Clipper. Cada ativo inclui uma ID de ativo/filtro do AMS, nome, URL de streaming publicada. Se aplicável, um token de autenticação de proteção de conteúdo ou uma matriz de URLs em miniatura pode ser passado. Se passadas, as miniaturas são populadas na interface. Em cenários em que a biblioteca de ativos é estática e pequena, você pode passar o contrato de ativos para cada ativo na biblioteca.

> [!NOTE]
> Ao carregar estaticamente os ativos no Clipper, os ativos são adicionados **diretamente à linha do tempo** e o **painel do ativo não é renderizado**. O primeiro ativo é adicionado à linha do tempo e o restante dos ativos é empilhado no lado direito da linha do tempo.

Para carregar uma biblioteca de ativos estáticos, use o método **Load** para passar uma representação JSON de cada ativo. O exemplo de código a seguir ilustra a representação JSON para um ativo.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> É recomendável encadear a chamada do método Load () com o método pronto (manipulador), conforme mostrado no exemplo anterior. O exemplo anterior garante que o widget esteja pronto antes de carregar os ativos.

> [!NOTE]
> Para que as URLs de miniatura funcionem conforme o esperado na linha do tempo do Clipper, elas devem ser distribuídas uniformemente pelo vídeo (com base na duração) e em ordem cronológica dentro da matriz. Você pode usar o seguinte trecho de predefinição JSON como uma referência de exemplo para gerar imagens com o processador ' Media Encoder Standard ':

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Carregar dinamicamente vídeos no Clipper
Carregue dinamicamente vídeos no Clipper para permitir que os usuários finais selecionem vídeos do painel de seleção de ativos para recortar.

Como alternativa, você pode carregar ativos dinamicamente por meio de um retorno de chamada. Em cenários em que os ativos são gerados dinamicamente ou a biblioteca é grande, você deve carregar por meio do retorno de chamada. Para carregar o ativo dinamicamente, você deve implementar a função de retorno de chamada onLoadAssets opcional. Essa função é passada para o Clipper na inicialização. Os ativos resolvidos devem aderir ao mesmo contrato que os ativos carregados estaticamente. O exemplo de código a seguir ilustra a assinatura do método, a entrada esperada e a saída esperada.

> [!NOTE]
> Ao carregar dinamicamente os ativos no Clipper, os ativos são renderizados no **painel de seleção de ativos**.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```
