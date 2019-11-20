---
title: Analisar arquivos de áudio e vídeo
titleSuffix: Azure Media Services
description: Saiba como analisar o conteúdo de áudio e vídeo usando o AudioAnalyzerPreset e o VideoAnalyzerPreset nos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: 23d546d6adcdb91b4ef4702b81fe77536fe9f3d3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186265"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analisar arquivos de vídeo e áudio com os serviços de mídia do Azure

Os serviços de mídia do Azure v3 permitem que você extraia informações de seus arquivos de vídeo e áudio com Video Indexer. Este artigo descreve as predefinições do analisador de serviços de mídia v3 usadas para extrair essas informações. Se quiser informações mais detalhadas, utilize diretamente o Video Indexer. Para entender quando usar as predefinições do Video Indexer vs. do Media Services Analyzer, confira o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Para analisar seu conteúdo usando as predefinições dos serviços de mídia v3, você cria uma **transformação** e envia um **trabalho** que usa uma dessas predefinições: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. Para obter um tutorial que demonstra como usar o **VideoAnalyzerPreset**, consulte [analisar vídeos com os serviços de mídia do Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando utilizar uma predefinição do Analisador de Vídeo ou Áudio, utilize o portal do Azure para definir a sua conta para ter 10 Unidades Reservadas de Multimédia S3. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Configurações predefinidas incorporadas

Os serviços de mídia atualmente dão suporte às seguintes predefinições do analisador interno:  

|**Nome da configuração predefinida**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analisando áudio|A predefinição aplica-se a um conjunto predefinido de operações de análise baseadas em ia, incluindo a transcrição de fala. Atualmente, a predefinição dá suporte ao processamento de conteúdo com uma única faixa de áudio que contém a fala em uma única linguagem. Você pode especificar o idioma para a carga de áudio na entrada usando o formato BCP-47 de ' marca de idioma-região '. Os idiomas com suporte são Inglês (' en-US ' e ' en-GB '), espanhol (' es-ES ' e ' es-MX '), francês (' fr-FR '), italiano (' it-IT '), japonês (' ja-JP '), Português (' pt-BR '), chinês (' ZH-CN '), alemão (' de-DE '), árabe (' ar-ex ' e ' ar-SY '), russo (' ru-RU '), Híndi (' hi-IN ') e coreano (' ko-KR ').<br/><br/> Se o idioma não for especificado ou definido como nulo, a detecção automática de idioma escolherá o primeiro idioma detectado e continuará com o idioma selecionado durante o arquivo. Atualmente, o recurso de detecção automática de idioma dá suporte a inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e Português. Ele não dá suporte à alternância dinâmica entre os idiomas após a detecção do primeiro idioma. O recurso de detecção automática de idioma funciona melhor com gravações de áudio com fala claramente discerníveis. Se a detecção automática de idioma falhar ao localizar o idioma, a transcrição retornará para o inglês.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analisando áudio e vídeo|Extrai informações (metadados avançados) de áudio e vídeo e gera um arquivo de formato JSON. Você pode especificar se deseja apenas extrair informações de áudio ao processar um arquivo de vídeo. Para obter mais informações, consulte [analisar vídeo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Detectando rostos presentes em vídeo|Descreve as configurações a serem usadas ao analisar um vídeo para detectar todas as faces presentes.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A predefinição permite que você extraia várias informações de áudio de um arquivo de áudio ou de vídeo. A saída inclui um arquivo JSON (com todas as informações) e o arquivo VTT para a transcrição de áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) . As informações de áudio incluem:

* **Transcrição de áudio**: uma transcrição das palavras faladas com carimbos de data/hora. Há suporte para vários idiomas.
* **Indexação do viva-voz**: um mapeamento dos alto-falantes e as palavras faladas correspondentes.
* **Análise de sentimentos de fala**: a saída da análise de sentimentos realizada na transcrição de áudio.
* **Palavras-** chave: palavras-chave que são extraídas da transcrição de áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A predefinição permite que você extraia várias informações de áudio e vídeo de um arquivo de vídeo. A saída inclui um arquivo JSON (com todas as informações), um arquivo VTT para a transcrição de vídeo e uma coleção de miniaturas. Essa predefinição também aceita uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) (representando o idioma do vídeo) como uma propriedade. As informações de vídeo incluem todas as informações de áudio mencionadas acima e os seguintes itens adicionais:

* **Acompanhamento de face**: o tempo durante o qual as faces estão presentes no vídeo. Cada face tem uma ID facial e uma coleção correspondente de miniaturas.
* **Texto visual**: o texto que é detectado por meio do reconhecimento óptico de caracteres. O texto é um carimbo de data/hora e também é usado para extrair palavras-chave (além da transcrição de áudio).
* **Quadros-chave**: uma coleção de quadros-chave extraídos do vídeo.
* **Moderação de conteúdo Visual**: a parte dos vídeos sinalizados como adulto ou erótico por natureza.
* **Anotação**: um resultado da anotação dos vídeos com base em um modelo de objeto predefinido

## <a name="insightsjson-elements"></a>elementos do insights. JSON

A saída inclui um arquivo JSON (insights. JSON) com todas as informações encontradas no vídeo ou áudio. O JSON pode conter os seguintes elementos:

### <a name="transcript"></a>transcriçõe

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

### <a name="ocr"></a>OCR

|Nome|Descrição|
|---|---|
|ID|A ID da linha de OCR.|
|texto|O texto de OCR.|
|confidencial|A confiança do reconhecimento.|
|language|O idioma do OCR.|
|instâncias|Uma lista de intervalos de tempo em que esse OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

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

### <a name="faces"></a>frente

|Nome|Descrição|
|---|---|
|ID|A ID da face.|
|nome|O nome da face. Pode ser ' desconhecido #0 ', um celebridade identificado ou uma pessoa treinada para o cliente.|
|confidencial|A confiança da identificação facial.|
|descrição|Uma descrição do celebridade. |
|thumbnailId|A ID da miniatura dessa face.|
|knownPersonId|A ID interna (se for uma pessoa conhecida).|
|referenceId|A ID do Bing (se for um celebridade do Bing).|
|referenceType|Atualmente, apenas Bing.|
|Título|O título (se for um celebridade — por exemplo, "CEO da Microsoft").|
|imageUrl|A URL da imagem, se for um celebridade.|
|instâncias|Instâncias em que a face apareceu no intervalo de tempo determinado. Cada instância também tem um thumbnailsId. |

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

### <a name="shots"></a>momentos

|Nome|Descrição|
|---|---|
|ID|A ID da captura.|
|keyFrames|Uma lista de quadros-chave dentro da captura (cada um tem uma ID e uma lista de instâncias de intervalos de tempo). As instâncias de quadros-chave têm um campo de miniaturaid com a ID de miniatura do quadro chave.|
|instâncias|Uma lista de intervalos de tempo desta captura (as capturas têm apenas 1 instância).|

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

### <a name="statistics"></a>estatística

|Nome|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o orador tem em um vídeo.|
|SpeakerLongestMonolog|A monolog mais longa do orador. Se o palestrante tiver silêncios dentro do monolog, ele será incluído. Silêncio no início e o fim do monolog são removidos.|
|SpeakerTalkToListenRatio|O cálculo é baseado no tempo gasto no monolog do orador (sem o silêncio entre eles) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentimentos

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

### <a name="labels"></a>etiquetas

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

### <a name="keywords"></a>palavras-chave

|Nome|Descrição|
|---|---|
|ID|A ID da palavra-chave.|
|texto|O texto da palavra-chave.|
|confidencial|A confiança de reconhecimento da palavra-chave.|
|language|O idioma da palavra-chave (quando traduzido).|
|instâncias|Uma lista de intervalos de tempo em que essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

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

O bloco visualContentModeration contém intervalos de tempo que Video Indexer encontrados para potencialmente ter conteúdo adulto. Se visualContentModeration estiver vazio, não haverá nenhum conteúdo adulto identificado.

Vídeos que são encontrados para conter conteúdo adulto ou erótico podem estar disponíveis somente para exibição particular. Os usuários podem enviar uma solicitação para uma revisão humana do conteúdo; nesse caso, o atributo `IsAdult` conterá o resultado da revisão humana.

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
## <a name="next-steps"></a>Passos seguintes

[Tutorial: analisar vídeos com os serviços de mídia do Azure](analyze-videos-tutorial-with-api.md)
