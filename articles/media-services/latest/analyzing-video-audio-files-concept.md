---
title: Analisar ficheiros de vídeo e áudio com os serviços de multimédia do Azure | Documentos da Microsoft
description: Ao utilizar os serviços de multimédia do Azure, pode analisar seu contnet de áudio e vídeo usando AudioAnalyzerPreset e VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 8f8af438d4034fc945a717fee0b720e3fe13cf56
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352005"
---
# <a name="analyzing-video-and-audio-files"></a>Analisar ficheiros de áudio e vídeos

Serviços de multimédia do Azure v3 permite-lhe extrair informações a partir dos seus ficheiros de áudio e vídeos com o indexador de vídeo através de configurações predefinidas do analisador de v3 de AMS (descritos neste artigo). Se quiser informações mais detalhadas, utilize diretamente o Video Indexer. Para saber como gostaria de utilizar o Video Indexer em relação a predefinições do analisador dos Serviços de Multimédia, consulte o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Para analisar seu conteúdo com configurações predefinidas do serviços de multimédia v3, crie uma **transformar** e submeter um **tarefa** que usa um destas predefinições: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. O seguinte artigo demonstra como usar **VideoAnalyzerPreset**: [Tutorial: Analisar vídeos com os serviços de multimédia do Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando utilizar uma predefinição do Analisador de Vídeo ou Áudio, utilize o portal do Azure para definir a sua conta para ter 10 Unidades Reservadas de Multimédia S3. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Configurações predefinidas incorporadas

Serviços de multimédia suportam atualmente pré-visualizando o analisador interno seguinte:  

|**Nome da configuração predefinida**|**Cenário**|**Detalhes**|
|---|---|---|
|**AudioAnalyzerPreset**|Análise de áudio|A configuração predefinida de aplica-se um conjunto predefinido de operações de análise baseada em IA, incluindo a transcrição de voz. Atualmente, a configuração predefinida suporta o processamento de conteúdo com uma faixa de áudio única que contém a voz num único idioma. Pode especificar o idioma para o payload de áudio na entrada com o formato de BCP 47 de "etiqueta de idioma-região". Idiomas suportados são do inglês (en-US e en-GB), Espanhol ("es-ES" e "es-MX"), francês ("fr-FR"), Italiano ("it-IT"), japonês ('ja-JP"), português ("pt-BR"), chinês ("zh-CN"), alemão ("de-DE"), Árabe ("ar-por exemplo,"), russo ('ru-RU"), Híndi ("hi-IN" ) e coreano ('ko-KR').<br/><br/> Se o idioma não está especificado ou definido como deteção de idioma de nulo, automática irá escolher o primeiro idioma detetado e processar com o idioma selecionado para a duração do ficheiro. A funcionalidade de deteção de idioma automática suporta, atualmente, inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Ele não suporta atualmente dinamicamente a alternância entre linguagens após o primeiro idioma é detetado. A funcionalidade de deteção de idioma automática funciona melhor com gravações de áudio com fala claramente discerníveis. Se a deteção de idioma automática não conseguir encontrar o idioma, a transcrição irá reverter para inglês.|
|**VideoAnalyzerPreset**|Análise de áudio e vídeo|Extrai informações (metadados avançados) de áudio e vídeo e produz um ficheiro de formato JSON. Pode especificar se pretende apenas extrair informações de áudio ao processar um ficheiro de vídeo. Para obter mais informações, consulte [analisar vídeo](analyze-videos-tutorial-with-api.md).|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

A configuração predefinida permite-lhe extrair várias informações de áudio de um arquivo de áudio ou vídeo. A saída inclui um ficheiro JSON (com as informações) e um arquivo VTT para a transcrição de áudio. Esta configuração predefinida aceita uma propriedade que especifica o idioma do ficheiro de entrada na forma de um [BCP47](https://tools.ietf.org/html/bcp47) cadeia de caracteres. As informações de áudio incluem:

* Transcrição de áudio – uma transcrição das palavras faladas carimbos de data. São suportados vários idiomas
* Orador indexação – um mapeamento dos oradores e as palavras faladas correspondentes
* Análise de sentimentos de voz – a saída de análise de sentimentos efetuada numa transcrição de áudio
* Palavras-chave – palavras-chave que são extraídas de transcrição de áudio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

A configuração predefinida permite-lhe extrair várias informações de áudio e vídeo de um ficheiro de vídeo. A saída inclui um ficheiro JSON (com as informações), um arquivo VTT para a transcrição de vídeo e uma coleção de miniaturas. Esta configuração predefinida também aceita um [BCP47](https://tools.ietf.org/html/bcp47) cadeia de caracteres (que representa o idioma do vídeo) como uma propriedade. As informações de vídeo incluem todas as informações de áudio mencionadas acima e os seguintes itens adicionais:

* Controlo de face – o tempo durante o qual rostos estão presentes no vídeo. Cada rosto tem um id de rostos e um conjunto correspondente de miniaturas
* Texto visual – o texto que é detetado por meio de reconhecimento ótico de carateres. O texto é tempo carimbadas e também utilizado para extrair as palavras-chave (além de transcrição áudio)
* Quadros-chave – uma coleção de keyframes que são extraídos do vídeo
* Moderação de conteúdos Visual – a parte dos vídeos que tenham sido sinalizados como adultos na natureza
* Anotação – um resultado de anotar os vídeos com base num modelo de objeto predefinidas

##  <a name="insightsjson-elements"></a>insights.json elements

A saída inclui um ficheiro JSON (insights.json) com todas as informações que foram encontradas do vídeo ou áudio. O json pode conter os seguintes elementos:

### <a name="transcript"></a>transcrição

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

### <a name="ocr"></a>OCR

|Name|Descrição|
|---|---|
|ID|O ID de linha de OCR.|
|texto|O texto de OCR.|
|confiança|A confiança de reconhecimento.|
|language|O idioma de OCR.|
|instâncias|Uma lista de intervalos de tempo em que este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

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

### <a name="faces"></a>faces

|Name|Descrição|
|---|---|
|ID|O ID do rosto.|
|nome|O nome de face. Pode ser ' desconhecido n º 0", uma celebridade identificada ou uma pessoa de preparação do cliente.|
|confiança|A confiança de identificação de face.|
|descrição|Uma descrição da celebridade. |
|thumbnailId|O ID da miniatura do que enfrentam.|
|knownPersonId|Se se trata de uma pessoa conhecida, sua ID de interno.|
|referenceId|Se for uma celebridade do Bing, o ID do Bing.|
|referenceType|Atualmente, apenas o Bing.|
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

### <a name="shots"></a>capturas de

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

### <a name="statistics"></a>Estatísticas

|Name|Descrição|
|---|---|
|CorrespondenceCount|Número de correspondências no vídeo.|
|WordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos o orador tem um vídeo.|
|SpeakerLongestMonolog|Monolog de mais longo do orador. Se o orador tiver silences dentro o monolog está incluído. Silence, no início e fim do monolog é removido.| 
|SpeakerTalkToListenRatio|O cálculo baseia-se o tempo gasto em monolog do orador (sem o silence entre) dividido pelo tempo total do vídeo. A hora é arredondada para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentimentos

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

### <a name="labels"></a>etiquetas

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

### <a name="keywords"></a>palavras-chave

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
## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Analisar vídeos com os serviços de multimédia do Azure](analyze-videos-tutorial-with-api.md)
