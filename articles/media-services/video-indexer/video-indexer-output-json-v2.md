---
title: Examine a saída do Indexer de Vídeo produzida pela V2 API - Azure
titleSuffix: Azure Media Services
description: Este tópico examina a produção do Indexer de Vídeo azure Media Services produzido pela V2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363943"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Examine a saída do Indexer de Vídeo produzida pela API

Quando ligar para a **API** do Índice de Vídeo Get e o estado de resposta estiver ok, obtém-se uma saída De JSON detalhada como conteúdo de resposta. O conteúdo da JSON contém detalhes das informações de vídeo especificadas. Os insights incluem: transcrições, OCRs, rostos, tópicos, blocos, etc. Cada tipo de insight inclui instâncias de intervalos de tempo que mostram quando a perceção aparece no vídeo. 

1. Para recuperar o ficheiro JSON, ligue para [obter API do Índice](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) de Vídeo
1. Se também estiver interessado em artefactos específicos, ligue para Obter Vídeo [Artifact Download URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Na chamada DaPi, especifique o tipo de artefacto solicitado (OCR, Faces, quadros-chave, etc.)

Também pode examinar visualmente as ideias resumidas do vídeo premindo o botão **Reproduzir** no vídeo no site do Indexer de [Vídeo.](https://www.videoindexer.ai/) Para mais informações, consulte [Ver e editar informações de vídeo](video-indexer-view-edit.md).

![Informações](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina o conteúdo jSON devolvido pela API do **Índice de Vídeo Get.** 

> [!NOTE]
> A expiração de todas as fichas de acesso no Indexer de Vídeo é de uma hora.


## <a name="root-elements"></a>Elementos de raiz

|Nome|Descrição|
|---|---|
|accountId|O ID da conta VI da lista de reprodução.|
|ID|A identificação da lista de reprodução.|
|nome|O nome da lista de reprodução.|
|descrição|A descrição da lista de reprodução.|
|userName|O nome do utilizador que criou a lista de reprodução.|
|criado|O tempo de criação da lista de reprodução.|
|privacyMode|O modo de privacidade da lista de reprodução (Privado/Público).|
|state|A lista de reprodução (carregada, processada, processada, falhada, em quarentena).|
|isOwned|Indica se a lista de reprodução foi criada pelo utilizador atual.|
|isEditable|Indica se o utilizador atual está autorizado a editar a lista de reprodução.|
|isBase|Indica se a lista de reprodução é uma lista de reprodução base (um vídeo) ou uma lista de reprodução feita de outros vídeos (derivado).|
|durationInSeconds|A duração total da lista de reprodução.|
|summarizedInsights|Contém um [SumreizedInsights](#summarizedinsights).
|vídeos|Uma lista de [vídeos](#videos) que constroem a lista de reprodução.<br/>Se esta lista de reprodução de intervalos de tempo construídos de outros vídeos (derivados), os vídeos desta lista conterão apenas dados dos intervalos de tempo incluídos.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Esta secção mostra o resumo dos insights.

|Atributo | Descrição|
|---|---|
|nome|O nome do vídeo. Por exemplo, Monitor Azure.|
|ID|A identificação do vídeo. Por exemplo, 63c6d532ff.|
|privacyMode|A sua avaria pode ter um dos seguintes modos: **Privado,** **Público**. **Público** - o vídeo é visível para todos na sua conta e para qualquer um que tenha um link para o vídeo. **Privado** - o vídeo é visível para todos na sua conta.|
|duration|Contém uma duração que descreve o tempo em que ocorreu uma perceção. A duração é em segundos.|
|thumbnailVideoId|A identificação do vídeo do qual a miniatura foi tirada.
|thumbnailId|A identificação da miniatura do vídeo. Para obter a miniatura real, ligue para [Get-Miniatura](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passe-a miniaturaVideoId e miniaturaId.|
|rostos|Pode conter zero ou mais rostos. Para obter informações mais detalhadas, consulte [rostos.](#faces)|
|palavras-chave|Pode conter zero ou mais palavras-chave. Para obter informações mais [detalhadas, consulte palavras-chave](#keywords).|
|sentimentos|Pode conter zero ou mais sentimentos. Para obter informações mais detalhadas, consulte [sentimentos.](#sentiments)|
|audioEfeitos| Pode conter zero ou mais áudioEffects. Para obter informações mais detalhadas, consulte [os efeitos áudio](#audioEffects).|
|etiquetas| Pode conter zero ou mais etiquetas. Para obter mais informações [detalhadas, consulte os rótulos.](#labels)|
|marcas| Pode conter zero ou mais marcas. Para obter informações mais detalhadas, consulte [as marcas.](#brands)|
|estatísticas | Para obter informações mais detalhadas, consulte [as estatísticas.](#statistics)|
|emoções| Pode conter zero ou mais emoções. Para obter informações mais detalhadas, consulte [as emoções.](#emotions)|
|tópicos|Pode conter zero ou mais tópicos. Os [tópicos](#topics) insight.|

## <a name="videos"></a>vídeos

|Nome|Descrição|
|---|---|
|accountId|O VÍDEO é vi account ID.|
|ID|A identificação do vídeo.|
|nome|O nome do vídeo.
|state|O estado do vídeo (carregado, processado, processado, falhado, colocado em quarentena).|
|processingProgress|O processamento progride durante o processamento (por exemplo, 20%).|
|falhaCódigo|O código de falha se não for processado (por exemplo, 'Ficheiro Não Suportado').|
|failureMessage|A mensagem de falha se não for processada.|
|externalId|O ID externo do vídeo (se especificado pelo utilizador).|
|externalUrl|O url externo do vídeo (se especificado pelo utilizador).|
|do IdP|Os metadados externos do vídeo (se especificados pelo utilizador).|
|isAdult|Indica se o vídeo foi revisto manualmente e identificado como um vídeo adulto.|
|insights|Os insights opõem-se. Para mais informações, consulte [insights.](#insights)|
|thumbnailId|A identificação da miniatura do vídeo. Para obter a miniatura real, ligue para [Get-Miniatura](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passe-o o ID de vídeo e miniatura.|
|publishedUrl|Uma url para transmitir o vídeo.|
|publishedUrlProxy|Uma url para transmitir o vídeo de (para dispositivos Apple).|
|viewToken|Um símbolo de visão de curta duração para transmitir o vídeo.|
|fonteIdioma|A linguagem de origem do vídeo.|
|language|A linguagem real do vídeo (tradução).|
|indexingPreset|O preset usado para indexar o vídeo.|
|streamingPreset|O preset usado para publicar o vídeo.|
|linguisticModelId|O modelo CRIS costumava transcrever o vídeo.|
|estatísticas | Para mais informações, consulte [as estatísticas.](#statistics)|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

Cada insight (por exemplo, linhas de transcrição, rostos, marcas, etc.), contém uma lista de elementos únicos (por exemplo, face1, face2, face3), e cada elemento tem os seus próprios metadados e uma lista dos seus casos (que são intervalos de tempo com metadados opcionais adicionais).

Um rosto pode ter uma identificação, um nome, uma miniatura, outros metadados, e uma lista dos seus casos temporais (por exemplo: 00:00:05 - 00:00:10, 00:01:00 - 00:02:30 e 00:41:21 - 00:41:49.) Cada instância temporal pode ter metadados adicionais. Por exemplo, as coordenadas de retângulo do rosto (20.230,60,60).

|Versão|A versão de código|
|---|---|
|fonteIdioma|A linguagem de origem do vídeo (assumindo uma linguagem principal). Na forma de uma corda [BCP-47.](https://tools.ietf.org/html/bcp47)|
|language|A linguagem insights (traduzida a partir da linguagem fonte). Na forma de uma corda [BCP-47.](https://tools.ietf.org/html/bcp47)|
|transcrição|A [transcrição.](#transcript)|
|ocr|A visão da [OCR.](#ocr)|
|palavras-chave|A visão [das palavras-chave.](#keywords)|
|Blocos|Pode conter um ou mais [blocos](#blocks)|
|rostos|A visão [dos rostos.](#faces)|
|etiquetas|A perceção dos [rótulos.](#labels)|
|tiros|A visão dos [tiros.](#shots)|
|marcas|As [marcas](#brands) são perspicazes.|
|audioEfeitos|A visão [do áudioEffects.](#audioEffects)|
|sentimentos|Os sentimentos são [perspicazes.](#sentiments)|
|visualContentModeration|A visão [ContentModeração](#visualcontentmoderation) insight.|
|textualContentModeration|A visão [textualContentModeration.](#textualcontentmoderation)|
|emoções| A perceção [das emoções.](#emotions)|
|tópicos|Os [tópicos](#topics) insight.|

Exemplo:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>Blocos

Atributo | Descrição
---|---
ID|Identificação do quarteirão.|
instâncias|Uma lista de intervalos de tempo deste quarteirão.|

#### <a name="transcript"></a>transcrição

|Nome|Descrição|
|---|---|
|ID|A identificação da linha.|
|texto|A transcrição em si.|
|language|A linguagem da transcrição. Destina-se a apoiar a transcrição onde cada linha pode ter uma linguagem diferente.|
|instâncias|Uma lista de intervalos de tempo onde esta linha apareceu. Se a instância for transcrição, terá apenas 1 instância.|

Exemplo:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>ocr

|Nome|Descrição|
|---|---|
|ID|O ID da linha OCR.|
|texto|O texto da OCR.|
|confiança|A confiança de reconhecimento.|
|language|A linguagem OCR.|
|instâncias|Uma lista de intervalos de tempo onde este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|
|altura|A altura do retângulo OCR|
|Início|A localização superior em px|
|esquerda| A localização esquerda em px|
|Largura|A largura do retângulo OCR|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|ID|A identificação da palavra-chave.|
|texto|O texto da palavra-chave.|
|confiança|A palavra-chave reconhece a confiança.|
|language|A linguagem da palavra-chave (quando traduzida).|
|instâncias|Uma lista de intervalos de tempo onde esta palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>rostos

|Nome|Descrição|
|---|---|
|ID|A identificação do rosto.|
|nome|O nome do rosto. Pode ser 'Unknown #0, uma celebridade identificada ou uma pessoa treinada por clientes.|
|confiança|A confiança na identificação facial.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|A identificação da miniatura daquele rosto.|
|knownPersonId|Se é uma pessoa conhecida, a sua identificação interna.|
|referenceId|Se é uma celebridade bing, é bing ID.|
|referenceType|Atualmente, só o Bing.|
|Título|Se for uma celebridade, o seu título (por exemplo, "CEO da Microsoft").|
|imageUrl|Se é uma celebridade, a sua url de imagem.|
|instâncias|Estes são casos de onde o rosto apareceu no intervalo de tempo dado. Cada instância também tem uma miniaturaId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>etiquetas

|Nome|Descrição|
|---|---|
|ID|A identificação da etiqueta.|
|nome|O nome da etiqueta (por exemplo, "Computador", "TV").).|
|language|A linguagem do nome do rótulo (quando traduzida). BCP-47|
|instâncias|Uma lista de intervalos de tempo onde esta etiqueta apareceu (uma etiqueta pode aparecer várias vezes). Cada caso tem um campo de confiança. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>cenas

|Nome|Descrição|
|---|---|
|ID|A identificação da cena.|
|instâncias|Uma lista de intervalos de tempo desta cena (uma cena só pode ter 1 instância).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>tiros

|Nome|Descrição|
|---|---|
|ID|A identificação do tiro.|
|keyFrames|Uma lista de keyFrames dentro do tiro (cada um tem um ID e uma lista de intervalos de tempo de instâncias). Cada instância keyFrame tem um campo miniaturaId, que contém o ID da miniatura do keyFrame.|
|instâncias|Uma lista de intervalos de tempo deste tiro (um tiro só pode ter 1 instância).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>marcas

Marcas de marcas de negócios e produtos detetadas no discurso para transcrição de texto e/ou Video OCR. Isto não inclui o reconhecimento visual de marcas ou deteção de logotipo.

|Nome|Descrição|
|---|---|
|ID|A identificação da marca.|
|nome|O nome das marcas.|
|referenceId | O sufixo da url da marca wikipedia. Por exemplo, "Target_Corporation" é o sufixo de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | A url da marca wikipedia, se existir. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|descrição|A descrição das marcas.|
|etiquetas|Uma lista de tags pré-definidas que foram associadas a esta marca.|
|confiança|O valor de confiança do detetor de marca Indexer de Vídeo (0-1).|
|instâncias|Uma lista de intervalos de tempo desta marca. Cada instância tem um brandType, que indica se esta marca apareceu na transcrição ou no OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>estatísticas

|Nome|Descrição|
|---|---|
|CorrespondênciaCount|Número de correspondências no vídeo.|
|SpeakerWordCount|O número de palavras por orador.|
|AltifalanteNumberOfFragments|A quantidade de fragmentos que o altifalante tem num vídeo.|
|SpeakerLongestMonolog|O monologmais mais longo do orador. Se o altifalante tiver silêncios no interior do monolog, está incluído. O silêncio no início e o fim do monologéno são removidos.| 
|SpeakerTalkToListenRatio|O cálculo baseia-se no tempo gasto no monolog (sem o silêncio pelo meio) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEfeitos

|Nome|Descrição|
|---|---|
|ID|O id de efeito áudio.|
|tipo|O tipo de efeito áudio (por exemplo, Palmas, Discurso, Silêncio).|
|instâncias|Uma lista de intervalos de tempo onde este efeito áudio apareceu.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentimentos

Os sentimentos são agregados pelo seu campo sentimentType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|Nome|Descrição|
|---|---|
|ID|A identificação do sentimento.|
|averageScore |A média de todas as pontuações de todos os casos desse tipo de sentimento - Positivo/Neutro/Negativo|
|instâncias|Uma lista de intervalos de tempo onde este sentimento apareceu.|
|sentimentoTipo |O tipo pode ser "Positivo", "Neutro" ou "Negativo".|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

O bloco visualContentModeration contém intervalos de tempo que o Indexer de Vídeo descobriu potencialmente ter conteúdo adulto. Se o visualContentModeration estiver vazio, não há conteúdo adulto que tenha sido identificado.

Os vídeos que se encontram com conteúdo adulto ou picante podem estar disponíveis apenas para visualização privada. Os utilizadores têm a opção de submeter um pedido de revisão humana do conteúdo, caso em que o atributo IsAdult conterá o resultado da revisão humana.

|Nome|Descrição|
|---|---|
|ID|O ID de moderação de conteúdo visual.|
|adultScore|A pontuação adulta (de moderador de conteúdo).|
|racyScore|A pontuação picante (da moderação do conteúdo).|
|instâncias|Uma lista de intervalos de tempo onde esta moderação de conteúdo visual apareceu.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Nome|Descrição|
|---|---|
|ID|O ID de moderação de conteúdo textual.|
|banidoWordsCount |O número de palavras proibidas.|
|banidoWordsRatio |A razão do número total de palavras.|

#### <a name="emotions"></a>emoções

O Indexer de vídeo identifica emoções baseadas em pistas de fala e áudio. A emoção identificada pode ser: alegria, tristeza, raiva ou medo.

|Nome|Descrição|
|---|---|
|ID|A identificação da emoção.|
|tipo|O momento de emoção que foi identificado com base em pistas de fala e áudio. A emoção pode ser: alegria, tristeza, raiva ou medo.|
|instâncias|Uma lista de intervalos de tempo onde esta emoção apareceu.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>tópicos

O Indexer de Vídeo faz inferência dos principais tópicos a partir de transcrições. Sempre que possível, a taxonomia [iPTC](https://iptc.org/standards/media-topics/) de 2º nível está incluída. 

|Nome|Descrição|
|---|---|
|ID|A identificação do tópico.|
|nome|O nome tópico, por exemplo: "Farmacêutica".|
|referenceId|Migalhas de pão refletindo a hierarquia dos tópicos. Por exemplo: "Saúde e bem-estar / Medicina e cuidados de saúde / Farmacêuticos".|
|confiança|A pontuação de confiança no intervalo [0,1]. Mais alto é mais confiante.|
|language|A linguagem usada no tema.|
|iptcName|O nome de código mediático IPTC, se detetado.|
|instâncias |Atualmente, o Indexer de Vídeo não indexa um tópico aos intervalos de tempo, pelo que todo o vídeo é usado como intervalo.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Passos seguintes

[Portal de Desenvolvimento de Indexer de Vídeo](https://api-portal.videoindexer.ai)

Para obter informações sobre como incorporar widgets na sua aplicação, consulte [widgets embed Video Indexer nas suas aplicações](video-indexer-embed-widgets.md). 

