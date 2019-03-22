---
title: Examine a saída do indexador de vídeo produzida pela v2 API
titlesuffix: Azure Media Services
description: Este tópico examina a saída do indexador de vídeo produzida pela v2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c0eedc32ee96c94b8b3621afc0ee211ed2ff19f5
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314880"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Examine a saída do indexador de vídeo produzida pela v2 API

> [!Note]
> A API do Video Indexer V1 foi preterida no dia 1 de agosto de 2018. Agora, deve utilizar a API do Video Indexer v2. <br/>Para programar com APIs do Video Indexer v2, veja as instruções que se encontram [aqui](https://api-portal.videoindexer.ai/). 

Quando chama a **índice de vídeo de introdução** API e o estado de resposta está OK, obtém uma saída JSON detalhada como o conteúdo de resposta. O conteúdo JSON contém detalhes de informações de vídeo especificadas. As informações incluem dimensões, como: transcrições, ocrs, rostos, tópicos, blocos, etc. As dimensões tem instâncias de intervalos de tempo que mostram quando cada dimensão apareceu no vídeo.  

Também visualmente pode examinar informações resumidas do vídeo ao premir o **reproduzir** botão no vídeo sobre o [Video Indexer](https://www.videoindexer.ai/) Web site. Para obter mais informações, consulte [ver e editar informações de vídeo](video-indexer-view-edit.md).

![Informações](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina o conteúdo JSON devolvido pelos **índice de vídeo de introdução** API. 

> [!NOTE]
> Expiração de todos os tokens de acesso no indexador de vídeo é uma hora.


## <a name="root-elements"></a>Elementos de raiz

|Name|Descrição|
|---|---|
|accountId|VI a lista de reprodução conta ID.|
|ID|ID de. a lista de reprodução|
|nome|Nome da lista de reprodução.|
|descrição|Descrição da lista de reprodução.|
|userName|O nome de utilizador que criou a lista de reprodução.|
|criado|Hora de criação da lista de reprodução.|
|privacyMode|Modo de privacidade para a lista de reprodução (público/privado).|
|state|A lista de reprodução (carregado, processamento, processados, com falhas, em quarentena).|
|isOwned|Indica se a lista de reprodução foi criada pelo utilizador atual.|
|isEditable|Indica se o utilizador atual tem autorização para editar a lista de reprodução.|
|isBase|Indica se a lista de reprodução é uma lista de reprodução base (um vídeo) ou uma lista de reprodução é constituído por outros vídeos (derivados).|
|durationInSeconds|A duração total da lista de reprodução.|
|summarizedInsights|Contém um [summarizedInsights](#summarizedinsights).
|vídeos|Uma lista de [vídeos](#videos) construir a lista de reprodução.<br/>Se esta lista de reprodução de construído de intervalos de tempo de outros vídeos (derivados), os vídeos nesta lista irá conter apenas os dados dos intervalos de tempo incluídos.|

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

Esta secção mostra o resumo das informações.

|Atributo | Descrição|
|---|---|
|nome|O nome do vídeo. Por exemplo, o Azure Monitor.|
|ID|O ID do vídeo. Por exemplo, 63c6d532ff.|
|privacyMode|Sua divisão pode ter um dos seguintes modos: **Privada**, **pública**. **Público** -o vídeo é visível para todas as pessoas na sua conta e qualquer pessoa que tenha uma ligação para o vídeo. **Privada** -o vídeo é visível para todas as pessoas na sua conta.|
|duração|Contém uma duração que descreve o tempo de que uma informação ocorreu. A duração é em segundos.|
|thumbnailVideoId|O ID do vídeo a partir do qual foi tirada a miniatura.
|thumbnailId|ID de miniatura do vídeo. Para obter a miniatura real, chamar Get-miniatura (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) e passá-lo thumbnailVideoId e thumbnailId.|
|faces|Pode conter zero ou mais rostos. Para obter mais informações, consulte [rostos](#faces).|
|palavras-chave|Pode conter zero ou mais palavras-chave. Para obter mais informações, consulte [palavras-chave](#keywords).|
|sentimentos|Pode conter zero ou mais sentimentos. Para obter mais informações, consulte [sentimentos](#sentiments).|
|audioEffects| Pode conter zero ou mais audioEffects. Para obter mais informações, consulte [audioEffects](#audioeffects).|
|etiquetas| Pode conter zero ou mais etiquetas. Para obter mais informações, consulte [etiquetas](#labels).|
|marcas| Pode conter zero ou mais marcas. Para obter mais informações, consulte [marcas](#brands).|
|Estatísticas | Para obter mais informações, consulte [estatísticas](#statistics).|
|emoções| Pode conter zero ou mais emoções. Para obter mais informações, consulte [emoções](#emotions).|
|Tópicos|Pode conter zero ou mais tópicos. O [tópicos](#topics) dimensão.|

## <a name="videos"></a>vídeos

|Name|Descrição|
|---|---|
|accountId|VI o vídeo conta ID.|
|ID|ID do vídeo|
|nome|Nome do vídeo.
|state|Estado do vídeo (carregado, processamento, processados, com falhas, em quarentena).|
|processingProgress|O progresso do processamento durante o processamento (por exemplo, 20%).|
|failureCode|O código de falha se processou (por exemplo, "UnsupportedFileType').|
|failureMessage|A mensagem de falha se não foi possível processar.|
|externalId|ID externo o vídeo (se especificado pelo utilizador).|
|externalUrl|Url externo o vídeo (se especificado pelo utilizador).|
|do IdP|Os metadados do vídeo externo (se especificado pelo utilizador).|
|isAdult|Indica se o vídeo foi revisado e identificado como um vídeo para adultos manualmente.|
|informações|O objeto de informações. Para obter mais informações, consulte [insights](#insights).|
|thumbnailId|ID de miniatura do vídeo. Para obter a chamada de miniatura real Get-miniatura (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) e transmita-o ID de vídeo e thumbnailId.|
|publishedUrl|Um url para transmitir o vídeo.|
|publishedUrlProxy|Um url para transmitir o vídeo de (para dispositivos da Apple).|
|viewToken|Um token de vista de resumo de duração para o vídeo de transmissão em fluxo.|
|sourceLanguage|Idioma de origem do vídeo.|
|language|Idioma da real do vídeo (tradução).|
|indexingPreset|A configuração predefinida utilizada para o vídeo de índice.|
|streamingPreset|A configuração predefinida utilizada para publicar o vídeo.|
|linguisticModelId|O modelo CRIS utilizado de transcrever o vídeo.|
|Estatísticas | Para obter mais informações, consulte [estatísticas](#statistics).|

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
### <a name="insights"></a>informações

As informações são um conjunto de dimensões (por exemplo, linhas de transcrição, rostos, marcas, etc.), onde cada dimensão é uma lista de elementos exclusivos (por exemplo, face1, face2, face3), e cada elemento tem seus próprios metadados e uma lista das suas instâncias (que são os intervalos de tempo com opcional metadados adicionais).

Um rosto pode ter um ID, um nome, uma miniatura, outros metadados e uma lista das suas instâncias temporais (por exemplo: 00: 00:05 – 00:00:10, 01: 00:00-00:02:30 e 00:41:21 – 00:41:49.) Cada instância temporal pode ter metadados adicionais. Por exemplo, o retângulo da face as coordenadas (20,230,60,60).

|Versão|A versão de código|
|---|---|
|sourceLanguage|Idioma de origem do vídeo (supondo que um idioma principal). Na forma de um [BCP 47](https://tools.ietf.org/html/bcp47) cadeia de caracteres.|
|language|O idioma de informações (traduzido do idioma de origem). Na forma de um [BCP 47](https://tools.ietf.org/html/bcp47) cadeia de caracteres.|
|transcrição|O [transcrição](#transcript) dimensão.|
|OCR|O [ocr](#ocr) dimensão.|
|palavras-chave|O [palavras-chave](#keywords) dimensão.|
|blocos|Pode conter um ou mais [blocos](#blocks)|
|faces|O [rostos](#faces) dimensão.|
|etiquetas|O [etiquetas](#labels) dimensão.|
|capturas de|O [capturas](#shots) dimensão.|
|marcas|O [marcas](#brands) dimensão.|
|audioEffects|O [audioEffects](#audioEffects) dimensão.|
|sentimentos|O [sentimentos](#sentiments) dimensão.|
|visualContentModeration|O [visualContentModeration](#visualcontentmoderation) dimensão.|
|textualContentModeration|O [textualContentModeration](#textualcontentmoderation) dimensão.|
|emoções| O [emoções](#emotions) dimensão.|
|Tópicos|O [tópicos](#topics) dimensão.|

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

#### <a name="blocks"></a>blocos

Atributo | Descrição
---|---
ID|ID do bloco.|
instâncias|Uma lista de intervalos de tempo deste bloco.|

#### <a name="transcript"></a>transcrição

|Name|Descrição|
|---|---|
|ID|O ID de linha.|
|texto|A transcrição em si.|
|language|O idioma de transcrição. A finalidade oferecer suporte a transcrição em que cada linha pode ter um idioma diferente.|
|instâncias|Uma lista de intervalos de tempo em que esta linha apareceu. Se a instância de transcrição, ele terá apenas 1 instância.|

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

|Name|Descrição|
|---|---|
|ID|O ID de linha de OCR.|
|texto|O texto de OCR.|
|confiança|A confiança de reconhecimento.|
|language|O idioma de OCR.|
|instâncias|Uma lista de intervalos de tempo em que este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|
|Altura|A altura do retângulo OCR|
|parte superior|A localização superior na px|
|À esquerda| A localização do esquerda no px|
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

|Name|Descrição|
|---|---|
|ID|O ID de palavra-chave.|
|texto|O texto de palavra-chave.|
|confiança|Confiança de reconhecimento da palavra-chave.|
|language|O idioma de palavra-chave (quando traduzido).|
|instâncias|Uma lista de intervalos de tempo em que esta palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="faces"></a>faces

|Name|Descrição|
|---|---|
|ID|O ID do rosto.|
|nome|O nome do mostrador da. Pode ser ' desconhecido n º 0, uma celebridade identificada ou uma pessoa de preparação do cliente.|
|confiança|A confiança de identificação de face.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|O ID da miniatura do que enfrentam.|
|knownPersonId|Se se trata de uma pessoa conhecida, sua ID de interno.|
|referenceId|Se for uma celebridade do Bing, o ID do Bing.|
|referenceType|Atualmente, apenas Bing.|
|título|Se for uma celebridade, seu título (por exemplo "CEO da Microsoft").|
|imageUrl|Se for uma celebridade, o seu url da imagem.|
|instâncias|Estes são instâncias de onde o mostrador apareceu no intervalo de tempo especificado. Cada instância tem também um thumbnailsId. |

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

|Name|Descrição|
|---|---|
|ID|O ID da etiqueta.|
|nome|O nome de etiqueta (por exemplo, "Computador", "Programas de TV").|
|language|Idioma de nome etiqueta (quando traduzido). BCP-47|
|instâncias|Uma lista de intervalos de tempo em que esta etiqueta apareceu (uma etiqueta pode aparecer várias vezes). Cada instância tem um campo de confiança. |


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

#### <a name="shots"></a>capturas de

|Name|Descrição|
|---|---|
|ID|O ID de captura.|
|keyFrames|Uma lista de quadros-chave dentro da captura (cada um tem um ID e uma lista de intervalos de tempo de instâncias). Instâncias de quadros-chave têm um campo de thumbnailId com miniatura do quadro-chave ID.|
|instâncias|Uma lista de intervalos de tempo desta captura (capturas de tem apenas 1 instância).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

#### <a name="brands"></a>marcas

Negócios e produto nomes de marca detetados na conversão de voz a transcrição de texto e/ou OCR de vídeo. Não inclui reconhecimento visual de marcas ou deteção de logótipo.

|Name|Descrição|
|---|---|
|ID|O ID da marca.|
|nome|O nome de marcas.|
|referenceId | O sufixo do url da wikipédia marca. Por exemplo, "Target_Corporation" é o sufixo de [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | A marca do url da Wikipédia, se existir. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|descrição|A descrição de marcas.|
|etiquetas|Uma lista de etiquetas predefinidas que foram associados essa marca.|
|confiança|O valor de confiança do detetor de marca do Video Indexer (0-1).|
|instâncias|Uma lista de intervalos de tempo desta marca. Cada instância possui um brandType, que indica se esta marca apareceu na transcrição ou no OCR.|

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

#### <a name="statistics"></a>Estatísticas

|Name|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|SpeakerWordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos o orador tem um vídeo.|
|SpeakerLongestMonolog|Monolog de mais longo do orador. Se o orador tiver silences dentro o monolog está incluído. Silence, no início e fim do monolog é removido.| 
|SpeakerTalkToListenRatio|O cálculo baseia-se o tempo gasto em monolog do orador (sem o silence entre) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|

#### <a name="audioeffects"></a>audioEffects

|Name|Descrição|
|---|---|
|ID|O ID do efeito de áudio.|
|tipo|O tipo de efeito de áudio (por exemplo, Clapping, conversão de voz, silêncio).|
|instâncias|Uma lista de intervalos de tempo em que esse efeito de áudio apareceu.|

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

Sentimentos são agregados pelo respetivo campo sentimentType (positivo/neutra/negativo). Por exemplo, 0-0.1, 0,1 0,2.

|Name|Descrição|
|---|---|
|ID|O ID de sentimentos.|
|averageScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimento - positivo/neutra/negativo|
|instâncias|Uma lista de intervalos de tempo em que esse sentimento apareceu.|
|sentimentType |O tipo pode ser "Neutral", 'Positivo' ou 'Negativo'.|

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

O bloco de visualContentModeration contém intervalos de tempo que o indexador de vídeo encontrado potencialmente ter conteúdo para adultos. Se visualContentModeration estiver vazio, não há nenhum conteúdo para adultos que foi identificado.

Vídeos que encontram-se para conter o conteúdo de adultos poderão estar disponíveis para ver privada apenas. Os utilizadores têm a opção para submeter um pedido para uma revisão de segurança do conteúdo, em que, caso o atributo IsAdult irá conter o resultado da revisão humana.

|Name|Descrição|
|---|---|
|ID|O ID de moderação de conteúdos visual.|
|adultScore|Como sendo para adultos (a partir do moderador de conteúdo).|
|racyScore|O classificado como indecoroso (a partir de moderação de conteúdos).|
|instâncias|Uma lista de intervalos de tempo em que este moderação de conteúdos visual apareceu.|

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

|Name|Descrição|
|---|---|
|ID|O ID de moderação de conteúdo textual.|
|bannedWordsCount |O número de palavras excluídas.|
|bannedWordsRatio |O rácio do número total de palavras.|

#### <a name="emotions"></a>emoções

O Video Indexer identifica emoções com base nas ajudas de voz e áudio. As emoções identificadas poderiam ser: alegria, tristeza, raiva ou medo.

|Name|Descrição|
|---|---|
|ID|O ID de emoções.|
|tipo|O momento de emoções que foi identificado com base em conversão de voz e áudio. As emoções podem ser alegria, tristeza, raiva ou medo.|
|instâncias|Uma lista de intervalos de tempo em que este emoções apareceram.|

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

O Video Indexer torna a inferência de tipos de tópicos principais de transcrições. Sempre que possível, o nível de dia 1 [IPTC](https://iptc.org/standards/media-topics/) taxonomia está incluída. 

|Name|Descrição|
|---|---|
|ID|O ID de tópico.|
|nome|O nome do tópico, por exemplo: "Farmacêutica".|
|referenceId|A estrutura de ligações que reflete a hierarquia de tópicos. Por exemplo: "Estado de funcionamento e wellbeing / medicina e saúde / farmacêutica".|
|confiança|A pontuação de confiança no intervalo [0,1]. Superior é mais confiança.|
|language|O idioma usado no tópico.|
|iptcName|O suporte de dados IPTC nome, de código detetada.|
|instâncias |Atualmente, o indexador de vídeos não indexa um tópico para intervalos de tempo, para que o vídeo inteiro é utilizado como o intervalo.|

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

## <a name="next-steps"></a>Passos Seguintes

[Portal de programador do indexador de vídeos](https://api-portal.videoindexer.ai)

Para obter informações sobre como incorporar widgets na sua aplicação, consulte [widgets de incorporar o Video Indexer nas suas aplicações](video-indexer-embed-widgets.md). 

