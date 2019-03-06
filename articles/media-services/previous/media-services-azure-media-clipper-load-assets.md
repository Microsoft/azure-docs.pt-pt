---
title: Carregar recursos para o Azure Media Clipper | Documentos da Microsoft
description: Passos para carregar recursos para o Azure Media Clipper
services: media-services
keywords: Clip; subclip; codificação; suporte de dados
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 2074f074d3212f790ddb8b4990b16edb3f204a93
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441326"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Carregamento de recursos no Azure Media Clipper  

Ativos podem ser carregados para o Azure Media Clipper por dois métodos:
1. Estaticamente passando uma biblioteca de ativos
2. Gerando dinamicamente uma lista de recursos através da API

## <a name="statically-load-videos-into-clipper"></a>Estaticamente carregar vídeos para Clipper
Estaticamente carregar vídeos para o Clipper para permitir que os utilizadores finais criar clips sem selecionar vídeos a partir do painel de seleção de elemento.

Neste caso, passar um conjunto estático de ativos para o Clipper. Cada ativo inclui um ID de recurso/filtragem do AMS, o URL de transmissão em fluxo do nome, publicado. Se aplicável, um token de autenticação de proteção de conteúdo ou uma matriz de miniatura URLs podem ser passados. Se passado, as miniaturas são preenchidas na interface. Em cenários em que a biblioteca de ativos é estático e pequeno porte, pode passar no contrato de recurso para cada elemento na biblioteca.

> [!NOTE]
> Ao carregar estaticamente ativos para o Clipper, os recursos são adicionados **diretamente para a linha cronológica** e o **painel de recurso não é processado**. O primeiro elemento é adicionado à linha cronológica e o restante dos ativos são empilhadas no lado direito da linha do tempo).

Para carregar uma biblioteca de ativos estático, utilize o **carregar** método passar uma representação JSON de cada ativo. O exemplo de código a seguir ilustra a representação JSON para um recurso.

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
> Recomenda-se para o método load() chamar com o método ready(handler), conforme mostrado no exemplo anterior de cadeia. O exemplo anterior garante que a widget é pronto antes de carregar os ativos.

> [!NOTE]
> Para a miniatura URLs a funcionar conforme esperado na linha da tempo de Clipper eles devem ser distribuídos uniformemente por vídeo (com base na duração) e, em ordem cronológica dentro da matriz. Pode utilizar o seguinte fragmento predefinido de JSON como uma referência de exemplo para gerar imagens com o processador "Codificador de multimédia Standard":

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

## <a name="dynamically-load-videos-in-clipper"></a>Carregar dinamicamente os vídeos no Clipper
Carregar dinamicamente vídeos para o Clipper para permitir que os utilizadores finais selecionar vídeos a partir do painel de seleção de recurso para recortar contra.

Em alternativa, pode carregar recursos dinamicamente por meio de um retorno de chamada. Em cenários em que ativos estão a ser gerados dinamicamente ou a biblioteca é grande, deve carregar por meio do retorno de chamada. Para carregar recursos dinamicamente, deve implementar a função de retorno de chamada onLoadAssets opcional. Esta função é passada para o Clipper na inicialização. Os recursos de resolvido devem seguir o mesmo contrato como ativos estaticamente carregá-lo. O exemplo de código a seguir ilustra a assinatura do método, a entrada esperada e a saída esperada.

> [!NOTE]
> Quando o carregamento dinâmico de recursos para o Clipper, os ativos são compostos na **painel de seleção de elemento**.

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
