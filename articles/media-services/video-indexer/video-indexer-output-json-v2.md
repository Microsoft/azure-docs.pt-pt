---
title: Examinar os serviços de mídia do Azure Video Indexer saída produzida pela API v2
titleSuffix: Azure Media Services
description: Este tópico examina a saída Video Indexer produzida pela API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 0a42c2dce3976a1bf83c85644f56f4c1d8abc9c8
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839545"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Examinar a saída de Video Indexer produzida pela API

Quando você chama a API **obter índice de vídeo** e o status da resposta é ok, você obtém uma saída JSON detalhada como o conteúdo da resposta. O conteúdo JSON contém detalhes das informações de vídeo especificadas. As informações incluem dimensões como: transcrições, OCRs, rostos, tópicos, blocos, etc. As dimensões têm instâncias de intervalos de tempo que mostram quando cada dimensão apareceu no vídeo.  

Você também pode examinar visualmente as informações resumidas do vídeo pressionando o botão **reproduzir** no vídeo no site [Video indexer](https://www.videoindexer.ai/) . Para obter mais informações, consulte [Exibir e editar informações de vídeo](video-indexer-view-edit.md).

![Informações](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina o conteúdo JSON retornado pela API **obter índice de vídeo** . 

> [!NOTE]
> A expiração de todos os tokens de acesso no Video Indexer é de uma hora.


## <a name="root-elements"></a>Elementos raiz

|Nome|Descrição|
|---|---|
|accountId|A ID da conta VI da playlist.|
|ID|A ID da playlist.|
|nome|O nome da playlist.|
|descrição|A descrição da playlist.|
|Usu|O nome do usuário que criou a lista de reprodução.|
|criação|O tempo de criação da playlist.|
|PrivacyMode|O modo de privacidade da playlist (privado/público).|
|state|A lista de reprodução (carregada, processando, processada, com falha, em quarentena).|
|Isdele|Indica se a playlist foi criada pelo usuário atual.|
|iseditável|Indica se o usuário atual está autorizado a editar a lista de reprodução.|
|IsBase|Indica se a playlist é uma lista de reprodução de base (um vídeo) ou uma lista de reprodução feita por outros vídeos (derivados).|
|durationInSeconds|A duração total da playlist.|
|summarizedInsights|Contém um [summarizedInsights](#summarizedinsights).
|explica|Uma lista de [vídeos](#videos) que constroem a playlist.<br/>Se essa lista de reprodução for construída de intervalos de tempo de outros vídeos (derivados), os vídeos nessa listagem conterão apenas os dados dos intervalos de tempo incluídos.|

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

Esta seção mostra o resumo das informações.

|Atributo | Descrição|
|---|---|
|nome|O nome do vídeo. Por exemplo, Azure Monitor.|
|ID|A ID do vídeo. Por exemplo, 63c6d532ff.|
|PrivacyMode|Sua divisão pode ter um dos seguintes modos: **privado**, **público**. **Público** -o vídeo é visível para todos em sua conta e qualquer pessoa que tenha um link para o vídeo. **Particular** -o vídeo fica visível para todos em sua conta.|
|permanência|Contém uma duração que descreve a hora em que ocorreu uma Insight. A duração é em segundos.|
|thumbnailVideoId|A ID do vídeo do qual a miniatura foi tirada.
|miniaturaid|A ID da miniatura do vídeo. Para obter a miniatura real, chame [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passe-o thumbnailVideoId e thumbnailid.|
|frente|Pode conter zero ou mais faces. Para obter informações mais detalhadas, consulte [faces](#faces).|
|Palavras-chave|Pode conter zero ou mais palavras-chave. Para obter informações mais detalhadas, consulte [palavras-chave](#keywords).|
|sentimentos|Pode conter zero ou mais sentimentos. Para obter informações mais detalhadas, consulte [sentimentos](#sentiments).|
|audioEffects| Pode conter zero ou mais audioEffects. Para obter informações mais detalhadas, consulte [audioEffects](#audioEffects).|
|las| Pode conter zero ou mais rótulos. Para obter mais informações, consulte [Rótulos](#labels).|
|expandir| Pode conter zero ou mais marcas. Para obter informações mais detalhadas, consulte [marcas](#brands).|
|estatística | Para obter informações mais detalhadas, consulte [estatísticas](#statistics).|
|emoções| Pode conter zero ou mais emoções. Para obter informações mais detalhadas, consulte [emoções](#emotions).|
|Tópicos|Pode conter zero ou mais tópicos. A dimensão de [Tópicos](#topics) .|

## <a name="videos"></a>explica

|Nome|Descrição|
|---|---|
|accountId|A ID da conta VI do vídeo.|
|ID|A ID do vídeo.|
|nome|O nome do vídeo.
|state|O estado do vídeo (carregado, processando, processado, com falha, em quarentena).|
|processingProgress|O progresso do processamento durante o processamento (por exemplo, 20%).|
|failureCode|O código de falha se o processo não for processado (por exemplo, ' UnsupportedFileType ').|
|failureMessage|A mensagem de falha se o processo não for processado.|
|externalId|A ID externa do vídeo (se especificado pelo usuário).|
|externalUrl|A URL externa do vídeo (se especificado pelo usuário).|
|do IdP|Os metadados externos do vídeo (se especificados pelo usuário).|
|isadulto|Indica se o vídeo foi revisado e identificado manualmente como um vídeo adulto.|
|insights|O objeto insights. Para obter mais informações, consulte [percepções](#insights).|
|miniaturaid|A ID da miniatura do vídeo. Para obter a chamada de miniatura real [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passá-la para a ID de vídeo e thumbnailid.|
|publishedUrl|Uma URL para transmitir o vídeo.|
|publishedUrlProxy|Uma URL para transmitir o vídeo (para dispositivos Apple).|
|viewToken|Um token de exibição de curta duração para transmitir o vídeo.|
|sourceLanguage|O idioma de origem do vídeo.|
|language|A linguagem real do vídeo (tradução).|
|indexingPreset|A predefinição usada para indexar o vídeo.|
|streamingPreset|A predefinição usada para publicar o vídeo.|
|linguisticModelId|O modelo CRIS usado para transcrever o vídeo.|
|estatística | Para obter mais informações, consulte [estatísticas](#statistics).|

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

As informações são um conjunto de dimensões (por exemplo, linhas de transcrição, rostos, marcas, etc.), em que cada dimensão é uma lista de elementos exclusivos (por exemplo, face1, face2, face3) e cada elemento tem seus próprios metadados e uma lista de suas instâncias (que são intervalos de tempo com metadados opcionais adicionais).

Uma face pode ter uma ID, um nome, uma miniatura, outros metadados e uma lista de suas instâncias temporais (por exemplo: 00:00:05 – 00:00:10, 00:01:00-00:02:30 e 00:41:21 – 00:41:49). Cada instância temporal pode ter metadados adicionais. Por exemplo, as coordenadas de retângulo da face (20230, 60, 60).

|Versão|A versão do código|
|---|---|
|sourceLanguage|O idioma de origem do vídeo (supondo um idioma mestre). Na forma de uma cadeia de caracteres [bcp-47](https://tools.ietf.org/html/bcp47) .|
|language|A linguagem de informações (convertida do idioma de origem). Na forma de uma cadeia de caracteres [bcp-47](https://tools.ietf.org/html/bcp47) .|
|transcriçõe|A dimensão de [transcrição](#transcript) .|
|OCR|A dimensão de [OCR](#ocr) .|
|Palavras-chave|A dimensão de [palavras-chave](#keywords) .|
|trava|Pode conter um ou mais [blocos](#blocks)|
|frente|A dimensão [faces](#faces) .|
|las|A dimensão de [Rótulos](#labels) .|
|momentos|A dimensão de [capturas](#shots) .|
|expandir|A dimensão de [marcas](#brands) .|
|audioEffects|A dimensão [audioEffects](#audioEffects) .|
|sentimentos|A dimensão de [opiniões](#sentiments) .|
|visualContentModeration|A dimensão [visualContentModeration](#visualcontentmoderation) .|
|textualContentModeration|A dimensão [textualContentModeration](#textualcontentmoderation) .|
|emoções| A dimensão [emoções](#emotions) .|
|Tópicos|A dimensão de [Tópicos](#topics) .|

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

#### <a name="blocks"></a>trava

Atributo | Descrição
---|---
ID|ID do bloco.|
instâncias|Uma lista de intervalos de tempo deste bloco.|

#### <a name="transcript"></a>transcriçõe

|Nome|Descrição|
|---|---|
|ID|A ID da linha.|
|texto|A transcrição em si.|
|language|O idioma da transcrição. Destina-se a dar suporte à transcrição, em que cada linha pode ter um idioma diferente.|
|instâncias|Uma lista de intervalos de tempo em que essa linha apareceu. Se a instância for transcrição, ela terá apenas uma instância.|

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

#### <a name="ocr"></a>OCR

|Nome|Descrição|
|---|---|
|ID|A ID da linha de OCR.|
|texto|O texto de OCR.|
|confidencial|A confiança do reconhecimento.|
|language|O idioma do OCR.|
|instâncias|Uma lista de intervalos de tempo em que esse OCR apareceu (o mesmo OCR pode aparecer várias vezes).|
|tamanho|A altura do retângulo OCR|
|Início|O local principal em px|
|mantida| O local à esquerda em px|
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

#### <a name="keywords"></a>Palavras-chave

|Nome|Descrição|
|---|---|
|ID|A ID da palavra-chave.|
|texto|O texto da palavra-chave.|
|confidencial|A confiança de reconhecimento da palavra-chave.|
|language|O idioma da palavra-chave (quando traduzido).|
|instâncias|Uma lista de intervalos de tempo em que essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

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

#### <a name="faces"></a>frente

|Nome|Descrição|
|---|---|
|ID|A ID da face.|
|nome|O nome da face. Pode ser ' desconhecido #0, um celebridade identificado ou uma pessoa treinada pelo cliente.|
|confidencial|A confiança da identificação facial.|
|descrição|Uma descrição do celebridade. |
|miniaturaid|A ID da miniatura dessa face.|
|knownPersonId|Se for uma pessoa conhecida, sua ID interna.|
|referenceId|Se for um Bing celebridade, sua ID do Bing.|
|referenceType|Atualmente, apenas Bing.|
|título|Se for um celebridade, seu título (por exemplo, "CEO da Microsoft").|
|imageUrl|Se for um celebridade, sua URL de imagem.|
|instâncias|Essas são instâncias de onde a aparência apareceu no intervalo de tempo determinado. Cada instância também tem um thumbnailsId. |

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

#### <a name="labels"></a>las

|Nome|Descrição|
|---|---|
|ID|A ID do rótulo.|
|nome|O nome do rótulo (por exemplo, ' computador ', ' TV ').|
|language|O nome do rótulo Language (quando traduzido). BCP-47|
|instâncias|Uma lista de intervalos de tempo em que esse rótulo apareceu (um rótulo pode aparecer várias vezes). Cada instância tem um campo de confiança. |


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
|ID|A ID da cena.|
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

#### <a name="shots"></a>momentos

|Nome|Descrição|
|---|---|
|ID|A ID da captura.|
|Quadros-chave|Uma lista de quadros-chave dentro da captura (cada um tem uma ID e uma lista de instâncias de intervalos de tempo). Cada instância de quadro-chave tem um campo de miniaturaid, que contém a ID de miniatura do quadro-chave.|
|instâncias|Uma lista de intervalos de tempo desta captura (uma captura pode ter apenas 1 instância).|

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

#### <a name="brands"></a>expandir

Nomes de marca comercial e de produto detectados na transcrição de fala para texto e/ou OCR de vídeo. Isso não inclui reconhecimento visual de marcas ou detecção de logotipo.

|Nome|Descrição|
|---|---|
|ID|A ID da marca.|
|nome|O nome das marcas.|
|referenceId | O sufixo da URL da wikipedia da marca. Por exemplo, "Target_Corporation" é o sufixo de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | A URL da Wikipédia da marca, se existir. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|descrição|A descrição das marcas.|
|etiquetas|Uma lista de marcas predefinidas que foram associadas a essa marca.|
|confidencial|O valor de confiança do detector de marca de Video Indexer (0-1).|
|instâncias|Uma lista de intervalos de tempo desta marca. Cada instância tem um brandtype, que indica se essa marca apareceu na transcrição ou no OCR.|

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

#### <a name="statistics"></a>estatística

|Nome|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|SpeakerWordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|A monolog mais longa do orador. Se o orador tiver silêncios dentro do monolog, ele será incluído. Silêncio no início e o fim do monolog são removidos.| 
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monolog do orador (sem o silêncio entre eles) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|Nome|Descrição|
|---|---|
|ID|A ID do efeito de áudio.|
|tipo|O tipo de efeito de áudio (por exemplo, Clapping, fala, silêncio).|
|instâncias|Uma lista de intervalos de tempo onde esse efeito de áudio apareceu.|

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

As opiniões são agregadas por seu campo Atfield (positivo/neutro/negativo). Por exemplo, 0-0,1, 0,1-0.2.

|Nome|Descrição|
|---|---|
|ID|A ID de sentimentos.|
|averageScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimentos-positivo/neutro/negativo|
|instâncias|Uma lista de intervalos de tempo em que esse sentimentos apareceu.|
|Resentitype |O tipo pode ser ' positivo ', ' neutro ' ou ' negativo '.|

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

O bloco visualContentModeration contém intervalos de tempo que Video Indexer encontrados para potencialmente ter conteúdo adulto. Se visualContentModeration estiver vazio, não haverá nenhum conteúdo adulto identificado.

Vídeos que são encontrados para conter conteúdo adulto ou erótico podem estar disponíveis somente para exibição particular. Os usuários têm a opção de enviar uma solicitação para uma revisão humana do conteúdo; nesse caso, o atributo isadulto conterá o resultado da revisão humana.

|Nome|Descrição|
|---|---|
|ID|A ID de moderação do conteúdo visual.|
|adultScore|A pontuação de adulto (do Content moderator).|
|racyScore|A pontuação de erótico (da moderação de conteúdo).|
|instâncias|Uma lista de intervalos de tempo onde essa moderação de conteúdo Visual apareceu.|

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
|ID|A ID de moderação do conteúdo textual.|
|bannedWordsCount |O número de palavras banidas.|
|bannedWordsRatio |A taxa do número total de palavras.|

#### <a name="emotions"></a>emoções

Video Indexer identifica emoções com base em indicações de fala e áudio. A emoção identificada poderia ser: Joy, tristeza, raiva ou medo.

|Nome|Descrição|
|---|---|
|ID|A ID de emoção.|
|tipo|O momento de emoção que foi identificado com base nas indicações de fala e áudio. A emoção poderia ser: alegria, tristeza, raiva ou medo.|
|instâncias|Uma lista de intervalos de tempo onde essa emoção apareceu.|

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

#### <a name="topics"></a>Tópicos

Video Indexer faz inferência de tópicos principais de transcrições. Quando possível, a taxonomia [IPTC](https://iptc.org/standards/media-topics/) de 2º nível é incluída. 

|Nome|Descrição|
|---|---|
|ID|A ID do tópico.|
|nome|O nome do tópico, por exemplo: "farmacêuticos".|
|referenceId|Trilhas que refletem a hierarquia de tópicos. Por exemplo: "saúde e wellbeing/medicina e saúde/farmacêuticos".|
|confidencial|A pontuação de confiança no intervalo [0, 1]. Mais alta é mais confiável.|
|language|O idioma usado no tópico.|
|iptcname|O nome do código de mídia IPTC, se detectado.|
|instâncias |Atualmente, Video Indexer não indexa um tópico para intervalos de tempo, de modo que o vídeo inteiro é usado como o intervalo.|

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

[Video Indexer portal do desenvolvedor](https://api-portal.videoindexer.ai)

Para obter informações sobre como inserir widgets em seu aplicativo, consulte [inserir Video indexer widgets em seus aplicativos](video-indexer-embed-widgets.md). 

