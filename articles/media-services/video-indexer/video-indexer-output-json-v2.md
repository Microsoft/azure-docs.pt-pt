---
title: Examinar a produção do Indexer de Vídeo produzido pela V2 API - Azure
titleSuffix: Azure Media Services
description: Este tópico analisa a produção do Azure Media Services Video Indexer produzido pela V2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530744"
---
# <a name="examine-the-video-indexer-output"></a>Examinar a saída do Indexador de Vídeo

Quando um vídeo é indexado, o Video Indexer produz o conteúdo JSON que contém detalhes das informações de vídeo especificadas. Os insights incluem: transcrições, TORs, rostos, tópicos, blocos, etc. Cada tipo de insight inclui instâncias de intervalos de tempo que mostram quando a perceção aparece no vídeo. 

Pode examinar visualmente as informações resumidas do vídeo premindo o botão **Reproduzir** no vídeo no site do [Indexer de Vídeo.](https://www.videoindexer.ai/) 

Também pode utilizar a API ligando para a API **do Índice de Vídeo Get** e o estado de resposta é OK, obtém uma saída JSON detalhada como conteúdo de resposta.

![Informações](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Este artigo examina a saída do Indexer de Vídeo (conteúdo JSON). <br/>Para obter informações sobre quais as funcionalidades e insights disponíveis para si, consulte [os insights do Video Indexer](video-indexer-overview.md#video-insights).

> [!NOTE]
> A expiração de todos os tokens de acesso no Indexante de Vídeo é de uma hora.

## <a name="get-the-insights"></a>Obtenha as ideias

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Insights/saída produzidos no site/portal

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Encontre um vídeo sobre a saída que pretende examinar.
1. Prima **Play** (Reproduzir).
1. Selecione o **separador Insights** (insights resumidos) ou o **separador Timeline** (permite filtrar os insights relevantes).
1. Descarregue artefactos e o que está neles.

Para obter mais informações, consulte [visualizar e editar informações de vídeo](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Insights/saída produzidos pela API

1. Para recuperar o ficheiro JSON, ligue para [obter API do Índice de Vídeo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Se você também estiver interessado em artefactos específicos, ligue [para Obter Video Artifact Download URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Na chamada API, especifique o tipo de artefacto solicitado (OCR, Faces, quadros-chave, etc.)

## <a name="root-elements-of-the-insights"></a>Elementos de raiz dos insights

|Nome|Descrição|
|---|---|
|accountId|ID da conta VI da lista de reprodução.|
|ID|A lista de reprodução identifica.|
|name|O nome da lista de reprodução.|
|descrição|A descrição da lista de reprodução.|
|userName|O nome do utilizador que criou a lista de reprodução.|
|criado|O tempo de criação da lista de reprodução.|
|privacidadeMode|O modo de privacidade da lista de reprodução (Privado/Público).|
|state|A lista de reprodução (carregada, processada, falhada, em quarentena).|
|isOwned|Indica se a lista de reprodução foi criada pelo utilizador atual.|
|é elegível|Indica se o utilizador atual está autorizado a editar a lista de reprodução.|
|é Base|Indica se a lista de reprodução é uma lista de reprodução base (um vídeo) ou uma lista de reprodução feita de outros vídeos (derivados).|
|duraçãoSso segundos|A duração total da lista de reprodução.|
|resumosInsights|Contém um [Resumo de Insights](#summarizedinsights).
|vídeos|Uma lista de [vídeos](#videos) a construir a lista de reprodução.<br/>Se esta lista de reprodução de intervalos de tempo construídos de outros vídeos (derivados), os vídeos desta lista conterão apenas dados dos intervalos de tempo incluídos.|

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

## <a name="summarizedinsights"></a>resumosInsights

Esta secção mostra o resumo dos insights.

|Atributo | Descrição|
|---|---|
|name|O nome do vídeo. Por exemplo, Monitor Azure.|
|ID|A identificação do vídeo. Por exemplo, 63c6d532ff.|
|privacidadeMode|A sua avaria pode ter um dos seguintes modos: **Privado,** **Público**. **Público** - o vídeo é visível para todos na sua conta e para qualquer um que tenha uma ligação com o vídeo. **Soldado** - o vídeo é visível para todos na sua conta.|
|duration|Contém uma duração que descreve a hora em que ocorreu uma visão. A duração é em segundos.|
|miniaturaVídeo|A identificação do vídeo a partir do qual a miniatura foi tirada.
|miniaturaId|A identificação da miniatura do vídeo. Para obter a miniatura real, ligue para [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passe-o em miniaturaVideoId e miniatura.|
|rostos/animadosCharacters|Pode conter zero ou mais rostos. Para obter informações mais detalhadas, consulte [rostos/animaçãoCharacters](#facesanimatedcharacters).|
|keywords|Pode conter zero ou mais palavras-chave. Para obter informações mais [detalhadas, consulte as palavras-chave](#keywords).|
|sentimentos|Pode conter zero ou mais sentimentos. Para obter informações mais [detalhadas, consulte os sentimentos.](#sentiments)|
|áudioSessos| Pode conter zero ou mais efeitos áudio. Para obter informações mais [detalhadas, consulte audioEffects](#audioeffects-public-preview).|
|rótulos| Pode conter zero ou mais etiquetas. Para obter mais informações [detalhadas, consulte as etiquetas.](#labels)|
|marcas| Pode conter zero ou mais marcas. Para obter informações mais detalhadas, consulte [as marcas.](#brands)|
|estatísticas | Para obter informações mais detalhadas, consulte [as estatísticas.](#statistics)|
|emoções| Pode conter zero ou mais emoções. Para obter informações mais [detalhadas, consulte as emoções.](#emotions)|
|tópicos|Pode conter zero ou mais tópicos. Os [tópicos introspeção.](#topics)|

## <a name="videos"></a>vídeos

|Nome|Descrição|
|---|---|
|accountId|A identificação da conta VI do vídeo.|
|ID|O vídeo é identificação.|
|name|O nome do vídeo.
|state|O estado do vídeo (carregado, processamento, processado, falhado, em quarentena).|
|processamentoProgress|O progresso do processamento durante o processamento (por exemplo, 20%).|
|falhaDesco|O código de avaria se não for processado (por exemplo, 'Não suportadoFileType').|
|falhaMessage|A mensagem de falha se não for processada.|
|externalId|O ID externo do vídeo (se especificado pelo utilizador).|
|externaUrl|O url externo do vídeo (se especificado pelo utilizador).|
|do IdP|Os metadados externos do vídeo (se especificado pelo utilizador).|
|isAdult|Indica se o vídeo foi analisado manualmente e identificado como um vídeo adulto.|
|insights|O objeto dos insights. Para mais informações, consulte [os insights.](#insights)|
|miniaturaId|A identificação da miniatura do vídeo. Para obter a chamada de miniatura [get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) e passar-lhe o ID de vídeo e miniatura.|
|publicadoUrl|Um url para transmitir o vídeo.|
|publicadoRrrPro procuração|Um url para transmitir o vídeo a partir (para dispositivos Apple).|
|viewToken|Um símbolo de visão curta para o streaming do vídeo.|
|fonteLanguage|A linguagem de origem do vídeo.|
|language|A linguagem real do vídeo (tradução).|
|indexingPreset|A predefinição usada para indexar o vídeo.|
|streamingPreset|A predefinição usada para publicar o vídeo.|
|modelId ling linguistic|O modelo CRIS usado para transcrever o vídeo.|
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

Um rosto pode ter uma identificação, um nome, uma miniatura, outros metadados, e uma lista dos seus casos temporais (por exemplo: 00:00:05 - 00:00:10, 00:01:00 - 00:02:30 e 00:41:21 – 00:41:49.) Cada instância temporal pode ter metadados adicionais. Por exemplo, as coordenadas do retângulo do rosto (20.230,60,60).

|Versão|A versão de código|
|---|---|
|fonteLanguage|A linguagem de origem do vídeo (assumindo uma língua principal). Na forma de uma corda [BCP-47.](https://tools.ietf.org/html/bcp47)|
|language|A linguagem insights (traduzida a partir da língua de origem). Na forma de uma corda [BCP-47.](https://tools.ietf.org/html/bcp47)|
|transcrição|A [transcrição.](#transcript)|
|ocr|A [visão do OCR.](#ocr)|
|keywords|A visão das [palavras-chave.](#keywords)|
|blocos|Pode conter um ou mais [blocos](#blocks)|
|rostos/animadosCharacters|Os [rostos/animadosCharacters](#facesanimatedcharacters) insight.|
|rótulos|A visão dos [rótulos.](#labels)|
|tiros|A [perspicácia](#shots) dos tiros.|
|marcas|A perceção das [marcas.](#brands)|
|áudioSessos|O [audioEffects](#audioeffects-public-preview) insights.|
|sentimentos|Os [sentimentos introspeção.](#sentiments)|
|visualContentModeration|A visão [visualContentModeration.](#visualcontentmoderation)|
|textualContentModeration|A [visão textualContentModeration.](#textualcontentmoderation)|
|emoções| A [perceção das emoções.](#emotions)|
|tópicos|Os [tópicos introspeção.](#topics)|
|altifalantes|A perceção [dos altifalantes.](#speakers)|

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
ID|Identificação do quarteirão.|
casos|Uma lista de intervalos de tempo deste quarteirão.|

#### <a name="transcript"></a>transcrição

|Nome|Descrição|
|---|---|
|ID|A identificação da linha.|
|texto|A transcrição em si.|
|confiança|A confiança da transcrição.|
|speakerId|A identificação do orador.|
|language|A linguagem da transcrição. Destina-se a apoiar a transcrição onde cada linha pode ter uma língua diferente.|
|casos|Uma lista de intervalos de tempo onde esta linha apareceu. Se o caso for transcrição, terá apenas 1 instância.|

Exemplo:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>ocr

|Nome|Descrição|
|---|---|
|ID|A identificação da linha OCR.|
|texto|O texto do OCR.|
|confiança|A confiança de reconhecimento.|
|language|A linguagem OCR.|
|casos|Uma lista de intervalos de tempo onde este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|
|altura|A altura do retângulo OCR|
|top|A localização superior em px|
|esquerda| A localização esquerda no PX|
|largura|A largura do retângulo OCR|

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

#### <a name="keywords"></a>keywords

|Nome|Descrição|
|---|---|
|ID|A identificação da palavra-chave.|
|texto|O texto da palavra-chave.|
|confiança|A confiança de reconhecimento da palavra-chave.|
|language|A linguagem da palavra-chave (quando traduzida).|
|casos|Uma lista de intervalos de tempo onde esta palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

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

#### <a name="facesanimatedcharacters"></a>rostos/animadosCharacters

`animatedCharacters` elemento substitui `faces` no caso de o vídeo ser indexado por um modelo de caracteres animados. Isto é feito usando um modelo personalizado em Visão Personalizada, O Indexer de Vídeo executa-o em quadros-chave.

Se os rostos (não personagens animados) estiverem presentes, o Video Indexer utiliza a API face em todos os quadros do vídeo para detetar rostos e celebridades.

|Nome|Descrição|
|---|---|
|ID|A identificação do rosto.|
|name|O nome do rosto. Pode ser 'Unknown #0, uma celebridade identificada ou uma pessoa treinada por um cliente.|
|confiança|A confiança de identificação facial.|
|descrição|Uma descrição da celebridade. |
|miniaturaId|A identificação da miniatura do rosto.|
|conhecidoPersonId|Se é uma pessoa conhecida, a sua identificação interna.|
|referenceId|Se é uma celebridade Bing, a sua identificação bing.|
|tipo de referência|Atualmente, só bing.|
|título|Se for uma celebridade, o seu título (por exemplo, "CEO da Microsoft").|
|imageUrl|Se é uma celebridade, a sua url de imagem.|
|casos|Estes são casos de onde o rosto apareceu no intervalo de tempo dado. Cada caso também tem uma miniatura. |

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

#### <a name="labels"></a>rótulos

|Nome|Descrição|
|---|---|
|ID|A identificação da etiqueta.|
|name|O nome da etiqueta (por exemplo, 'Computador', 'TV').|
|language|O nome do rótulo (quando traduzido). BCP-47|
|casos|Uma lista de intervalos de tempo onde esta etiqueta apareceu (uma etiqueta pode aparecer várias vezes). Cada caso tem um campo de confiança. |


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
|casos|Uma lista de intervalos de tempo desta cena (uma cena só pode ter 1 instância).|

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
|keyFrames|Uma lista de quadros-chave dentro do tiro (cada um tem um ID e uma lista de intervalos de tempo de instâncias). Cada instância do KeyFrame tem um campo miniatura, que contém o ID da miniatura do KeyFrame.|
|casos|Uma lista de intervalos de tempo deste tiro (um tiro só pode ter 1 instância).|

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

Nomes de marcas de negócios e produtos detetados no discurso para transcrição de texto e/ou vídeo OCR. Isto não inclui o reconhecimento visual de marcas ou deteção de logotipos.

|Nome|Descrição|
|---|---|
|ID|A identificação da marca.|
|name|O nome das marcas.|
|referenceId | O sufixo da marca wikipedia url. Por exemplo, "Target_Corporation" é o sufixo de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referênciaUrl | A url da marca wikipedia, se existe. Por exemplo, [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|descrição|A descrição das marcas.|
|etiquetas|Uma lista de tags predefinidas que estavam associadas a esta marca.|
|confiança|O valor de confiança do detetor de marca Video Indexer (0-1).|
|casos|Uma lista de intervalos de tempo desta marca. Cada instância tem uma marcaType, o que indica se esta marca apareceu na transcrição ou no OCR.|

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
|Conta de Correspondência|Número de correspondências no vídeo.|
|SpeakerWordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o altifalante tem num vídeo.|
|OradorLongestMonolog|O maior monótono do orador. Se o altifalante tiver silêncios dentro do monótono, está incluído. O silêncio no início e no fim do monóloga é removido.| 
|SpeakerTalkToListenRatio|O cálculo baseia-se no tempo gasto no monótono do orador (sem o silêncio no meio) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|

#### <a name="audioeffects-public-preview"></a>audioEffects (pré-visualização pública)

|Nome|Descrição
|---|---|
|ID|O ID do efeito áudio|
|tipo|O tipo de efeito áudio|
|casos|Uma lista de intervalos de tempo onde este efeito áudio apareceu. Cada caso tem um campo de confiança.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentimentos

Os sentimentos são agregados pelo seu campo de sentimentoType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|Nome|Descrição|
|---|---|
|ID|A identificação do sentimento.|
|médiaScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimento - Positivo/Neutro/Negativo|
|casos|Uma lista de intervalos de tempo onde este sentimento apareceu.|
|sentimentType |O tipo pode ser 'Positivo', 'Neutro', ou 'Negativo'.|

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

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer descobriu potencialmente ter conteúdo adulto. Se o visualContentModeration estiver vazio, não há nenhum conteúdo adulto que tenha sido identificado.

Os vídeos que contêm conteúdo adulto ou picante podem estar disponíveis apenas para visualização privada. Os utilizadores têm a opção de apresentar um pedido de revisão humana do conteúdo, caso em que o atributo IsAdult conterá o resultado da revisão humana.

|Nome|Descrição|
|---|---|
|ID|O ID de moderação de conteúdo visual.|
|adultScore|A pontuação adulta (do moderador de conteúdo).|
|racyScore|A pontuação picante (da moderação do conteúdo).|
|casos|Uma lista de intervalos de tempo onde esta moderação de conteúdo visual apareceu.|

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
|bannedWordsCount |O número de palavras proibidas.|
|bannedWordsRatio |A razão do número total de palavras.|

#### <a name="emotions"></a>emoções

O Indexer de Vídeo identifica emoções baseadas em sinais de fala e áudio. A emoção identificada pode ser: alegria, tristeza, raiva ou medo.

|Nome|Descrição|
|---|---|
|ID|A identificação da emoção.|
|tipo|O momento de emoção que foi identificado com base em sinais de fala e áudio. A emoção pode ser: alegria, tristeza, raiva ou medo.|
|casos|Uma lista de intervalos de tempo onde esta emoção apareceu.|

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

O Indexante de Vídeo faz inferência dos principais tópicos a partir de transcrições. Quando possível, a taxonomia [IPTC](https://iptc.org/standards/media-topics/) de 2º nível está incluída. 

|Nome|Descrição|
|---|---|
|ID|A identificação do tópico.|
|name|O nome do tópico, por exemplo: "Farmacêutica".|
|referenceId|Migalhas de pão que refletem a hierarquia dos tópicos. Por exemplo: "Saúde e bem-estar / Medicina e cuidados de saúde /Farmacêuticos".|
|confiança|A pontuação de confiança no intervalo [0,1]. Mais alto é mais confiante.|
|language|A linguagem usada no tópico.|
|iptcName|O nome do código de comunicação IPTC, se for detetado.|
|casos |Atualmente, o Video Indexer não indexa um tópico aos intervalos de tempo, pelo que todo o vídeo é usado como intervalo.|

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

#### <a name="speakers"></a>altifalantes

|Nome|Descrição|
|---|---|
|ID|A identificação do altifalante.|
|name|O nome do altifalante sob a forma de "Speaker # *<number>* " Por exemplo: "Orador #1".|
|casos |Uma lista de intervalos de tempo onde este orador apareceu.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Passos seguintes

[Portal do Desenvolvedor de Indexados de Vídeo](https://api-portal.videoindexer.ai)

Para obter informações sobre como incorporar widgets na sua aplicação, consulte [widgets do Indexer de Vídeo Incorporado nas suas aplicações](video-indexer-embed-widgets.md). 

