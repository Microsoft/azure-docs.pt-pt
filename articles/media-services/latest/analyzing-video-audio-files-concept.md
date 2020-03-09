---
title: Analisar ficheiros de vídeo e áudio
titleSuffix: Azure Media Services
description: Saiba como analisar conteúdos de áudio e vídeo utilizando AudioAnalyzerPreset e VideoAnalyzerPreset nos Serviços De Mídia Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359462"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analise ficheiros de vídeo e áudio com a Azure Media Services

O Azure Media Services v3 permite extrair informações dos seus ficheiros de vídeo e áudio com o Indexer de Vídeo. Este artigo descreve as predefinições de analisador de Media Services v3 usadas para extrair esses insights. Se quiser informações mais detalhadas, utilize diretamente o Video Indexer. Para entender quando utilizar os predefinições do analisador de vídeo vs. Media Services, confira o documento de [comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Para analisar o seu conteúdo utilizando predefinições de Media Services v3, cria um **Transform** e envia um **Trabalho** que utiliza uma destas predefinições: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. Para um tutorial que demonstre como usar **o VideoAnalyzerPreset,** consulte vídeos de análise com os [Serviços De Mídia Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando utilizar uma predefinição do Analisador de Vídeo ou Áudio, utilize o portal do Azure para definir a sua conta para ter 10 Unidades Reservadas de Multimédia S3. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um lembrete importante, você deve cumprir todas as leis aplicáveis na sua utilização do Indexer de Vídeo, e você não pode usar O Indexer de Vídeo ou qualquer outro serviço Azure de uma forma que viole os direitos de outros ou pode ser prejudicial para os outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço de Indexer de Vídeo para processamento e armazenamento, deve ter todos os direitos adequados, incluindo todos os consentimentos adequados, do(s) indivíduo(s) no vídeo. Para conhecer a conformidade, privacidade e segurança no Indexer de Vídeo, os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)da Microsoft . Para as obrigações de privacidade da Microsoft e o tratamento dos seus dados, por favor, consulte a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os Termos dos [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") e a [Adenda](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) de Processamento de Dados ("DPA"). Informações adicionais sobre a privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao utilizar o Indexer de Vídeo, concorda em ficar vinculado aos Termos dos Serviços Cognitivos, ao OST, dPA e à Declaração de Privacidade.

## <a name="built-in-presets"></a>Configurações predefinidas incorporadas

Atualmente, a Media Services suporta as seguintes predefinições de analisadores incorporados:  

|**Nome predefinido**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisar áudio|O predefinido aplica um conjunto predefinido de operações de análise baseadas em IA, incluindo a transcrição da fala. Atualmente, o preset suporta o processamento de conteúdo com uma única faixa de áudio que contém a fala numa única língua. Pode especificar o idioma para a carga áudio na entrada utilizando o formato BCP-47 de "região de tag-região linguística". As línguas apoiadas são o inglês ('en-US' e 'en-GB'), o espanhol ('es-ES' e 'es-MX'), o francês ('fr-FR'), o italiano ('it-IT'), o japonês ('ja-JP'), o português ('pt-BR'), o chinês ('zh-CN'), o alemão ('de-de-DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-R'), o alemão ('de-de-DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-R'), o alemão ('de-de-DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-BR'), o alemão ('de-de-DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-BR'), o alemão ('de-de-DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-BR'), o alemão ('de-de-DE'DE'), o árabe ('ar-EG' e 'ar-SY'), o russo ('pt-BR'), o alemão ('de-de-DE'), o árabe Hindi ('hi-IN'), e coreano ('ko-KR').<br/><br/> Se o idioma não for especificado ou definido para nula, a deteção automática de idiomas escolhe a primeira língua detetada e continua com o idioma selecionado durante a duração do ficheiro. A funcionalidade de deteção automática de línguas suporta atualmente inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Não suporta alternar dinamicamente entre línguas após a primeira língua ser detetada. A funcionalidade de deteção automática de idiomas funciona melhor com gravações áudio com uma fala claramente percetível. Se a deteção automática de idiomas não encontrar a língua, a transcrição remonta ao inglês.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisar áudio e vídeo|Extrai insights (metadados ricos) tanto de áudio como de vídeo, e produz um ficheiro de formato JSON. Pode especificar se apenas pretende extrair informações áudio ao processar um ficheiro de vídeo. Para mais informações, consulte [O Vídeo Análise](analyze-videos-tutorial-with-api.md).|
|[Predefinição faceDetectorPre](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Detetar rostos presentes em vídeo|Descreve as definições a utilizar ao analisar um vídeo para detetar todos os rostos presentes.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

O preset permite extrair várias informações áudio de um ficheiro de áudio ou vídeo. A saída inclui um ficheiro JSON (com todas as informações) e ficheiro VTT para a transcrição áudio. Este preset aceita um imóvel que especifica a linguagem do ficheiro de entrada sob a forma de uma cadeia [BCP47.](https://tools.ietf.org/html/bcp47) As informações áudio incluem:

* **Transcrição**áudio : Uma transcrição das palavras faladas com carimbos temporais. Várias línguas são apoiadas.
* **Indexação do altifalante**: Um mapeamento dos altifalantes e das palavras faladas correspondentes.
* **Análise**do sentimento da fala : A produção da análise de sentimento realizada na transcrição áudio.
* **Palavras-chave**: Palavras-chave extraídas da transcrição áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

O preset permite extrair várias informações de áudio e vídeo de um ficheiro de vídeo. A saída inclui um ficheiro JSON (com todas as informações), um ficheiro VTT para a transcrição de vídeo e uma coleção de miniaturas. Este preset também aceita uma cadeia [BCP47](https://tools.ietf.org/html/bcp47) (representando a linguagem do vídeo) como um imóvel. As informações de vídeo incluem todas as informações áudio acima mencionadas e os seguintes itens adicionais:

* **Rastreio facial**: O tempo durante o qual as faces estão presentes no vídeo. Cada rosto tem um rosto ID e uma coleção correspondente de miniaturas.
* **Texto visual**: O texto detetado através do reconhecimento ótico de caracteres. O texto é carimbado no tempo e também utilizado para extrair palavras-chave (além da transcrição áudio).
* **Quadros-chave**: Uma coleção de quadros-chave extraídos do vídeo.
* **Moderação de conteúdo visual**: A parte dos vídeos sinalizados como adulto ou picante na natureza.
* **Anotação**: Resultado de anotar os vídeos com base num modelo de objeto pré-definido

## <a name="insightsjson-elements"></a>insights.json elementos

A saída inclui um ficheiro JSON (insights.json) com todas as informações encontradas no vídeo ou áudio. O JSON pode conter os seguintes elementos:

### <a name="transcript"></a>transcrição

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

### <a name="ocr"></a>ocr

|Nome|Descrição|
|---|---|
|ID|O ID da linha OCR.|
|texto|O texto da OCR.|
|confiança|A confiança de reconhecimento.|
|language|A linguagem OCR.|
|instâncias|Uma lista de intervalos de tempo onde este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>rostos

|Nome|Descrição|
|---|---|
|ID|A identificação do rosto.|
|nome|O nome do rosto. Pode ser 'Unknown #0', uma celebridade identificada, ou uma pessoa treinada por clientes.|
|confiança|A confiança na identificação facial.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|A identificação da miniatura daquele rosto.|
|knownPersonId|A identificação interna (se for uma pessoa conhecida).|
|referenceId|O Bing ID (se for uma celebridade bing).|
|referenceType|Atualmente, só o Bing.|
|Título|O título (se for uma celebridade - por exemplo, "CEO da Microsoft").|
|imageUrl|A URL de imagem, se for uma celebridade.|
|instâncias|Casos em que o rosto apareceu no intervalo de tempo dado. Cada instância também tem uma miniaturaId. |

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

### <a name="shots"></a>tiros

|Nome|Descrição|
|---|---|
|ID|A identificação do tiro.|
|keyFrames|Uma lista de quadros-chave dentro do filme (cada um tem um ID e uma lista de intervalos de tempo de instâncias). Os quadros-chave têm um campo miniaturaId com o ID da miniatura do keyFrame.|
|instâncias|Uma lista de intervalos de tempo deste tiro (os tiros têm apenas 1 instância).|

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

### <a name="statistics"></a>estatísticas

|Nome|Descrição|
|---|---|
|CorrespondênciaCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por orador.|
|AltifalanteNumberOfFragments|A quantidade de fragmentos que o altifalante tem num vídeo.|
|SpeakerLongestMonolog|O monologmais mais longo do orador. Se o altifalante tiver silêncios dentro do monolog, está incluído. O silêncio no início e o fim do monologéno são removidos.|
|SpeakerTalkToListenRatio|O cálculo baseia-se no tempo gasto no monolog (sem o silêncio pelo meio) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentimentos

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

### <a name="labels"></a>etiquetas

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

### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|ID|A identificação da palavra-chave.|
|texto|O texto da palavra-chave.|
|confiança|A palavra-chave reconhece a confiança.|
|language|A linguagem da palavra-chave (quando traduzida).|
|instâncias|Uma lista de intervalos de tempo onde esta palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

O bloco visualContentModeration contém intervalos de tempo que o Indexer de Vídeo descobriu potencialmente ter conteúdo adulto. Se o visualContentModeração estiver vazio, não há conteúdo adulto que tenha sido identificado.

Os vídeos que se encontram com conteúdo adulto ou picante podem estar disponíveis apenas para visualização privada. Os utilizadores podem submeter um pedido de revisão humana do conteúdo, caso em que o atributo `IsAdult` conterá o resultado da revisão humana.

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
## <a name="next-steps"></a>Passos seguintes

[Tutorial: Analisar vídeos com a Azure Media Services](analyze-videos-tutorial-with-api.md)
