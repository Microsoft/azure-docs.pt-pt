---
title: Submeter tarefas de recorte do Azure Media Clipper | Documentos da Microsoft
description: Passos para submeter tarefas de recorte do Azure Media Clipper
services: media-services
keywords: Clip; subclip; codificação; suporte de dados
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a6f6033fb52c54cf3f9987743fcd725e3539803
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453801"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Submeter tarefas de recorte de Azure Media Clipper 

Azure Media Clipper requer uma **submitSubclipCallback** método a ser implementada para processar a submissão de tarefas de recorte. Esta função é para a implementação de um POST de HTTP de saída Clipper a um serviço web. Este serviço web é onde pode submeter a tarefa de codificação. O resultado a Clipper são ambos um codificador de multimédia Standard codificação configuração predefinida para tarefas compostas ou o payload de REST API para chamadas de filtro do manifesto dinâmico. Esse modelo pass-through é necessário porque as credenciais de conta de serviços de multimédia não são seguras no navegador do cliente.

O diagrama de sequência seguinte ilustra o fluxo de trabalho entre o cliente de navegador, o seu serviço web e serviços de multimédia do Azure: ![Diagrama de sequência do Azure Media Clipper](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

No diagrama anterior, as entidades de quatro são: navegador do usuário final, seu serviço web, o ponto final da CDN que aloja os recursos de Clipper e dos serviços de multimédia do Azure. Quando o utilizador final navega para sua página da web, a página obtém os recursos CSS e Clipper JavaScript do alojamento ponto final da CDN. O utilizador final configura a tarefa de recorte ou chamada de criação de filtro do manifesto dinâmico em seus navegadores. Quando o utilizador final envia a chamada de criação da tarefa ou um filtro, o browser coloca a carga de trabalho para um serviço web que tem de implementar. Este serviço web, por fim, submete o trabalho de recorte ou as credenciais da conta dos serviços de chamada de criação de filtro para os serviços de multimédia do Azure utilizando o suporte de dados.

O exemplo de código a seguir ilustra um exemplo **submitSubclipCallback** método. Nesse método, implementa o HTTP POST da predefinição de codificação Media Encoder Standard. Se a POSTAGEM foi concluída com êxito (**resultado**), o **promessa** for resolvido, caso contrário, ele é rejeitado com detalhes do erro.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
A saída de submissão de tarefas é a predefinição de codificação Media Encoder Standard para a tarefa composta ou o payload de REST API para filtros do manifesto dinâmico.

## <a name="submitting-encoding-job-to-create-video"></a>A submeter a tarefa de codificação para criar o vídeo
Pode submeter uma tarefa de codificação Media Encoder Standard para criar um quadro preciso clip de vídeo. Codificação de produzir de tarefa processado vídeos, um novo fragmentados ficheiro MP4.

O contrato de saída de tarefa para recorte composto é um objeto JSON com as seguintes propriedades:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Para executar a tarefa de codificação, submeter o Media Encoder Standard tarefa de codificação associada com configuração predefinida. Consulte este artigo para obter detalhes sobre como submeter a codificação de tarefas com o [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) ou [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Criação rápida de clips de vídeo sem codificação
Alternativa para a criação de uma tarefa de codificação, pode utilizar o Azure Media Clipper para criar filtros do manifesto dinâmico. Filtros não necessitam de codificação e podem ser criados rapidamente, pois não é criado um novo recurso. O contrato de saída para um recorte de filtro é um objeto JSON com as seguintes propriedades:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Para submeter a chamada REST para criar o filtro do manifesto dinâmico, submeta o payload de filtro associado através da [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
