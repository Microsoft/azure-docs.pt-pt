---
title: Analisar ficheiros de vídeo e áudio
description: Saiba como analisar conteúdos de áudio e vídeo utilizando AudioAnalyzerPreset e VideoAnalyzerPreset em Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 25b5c3163b657633ca78215468f4c2a2e40949b7
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897229"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analise ficheiros de vídeo e áudio com a Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Services v3 permite extrair informações dos seus ficheiros de vídeo e áudio com o Video Indexer. Este artigo descreve as predefinições de analisadores de media v3 usadas para extrair esses insights. Se quiser informações mais detalhadas, utilize diretamente o Video Indexer. Para entender quando utilizar o Video Indexer vs. Media Services analise predefins, consulte o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Existem dois modos para a predefinição do Analisador de Áudio, básico e padrão. Consulte a descrição das diferenças na tabela abaixo.

Para analisar o seu conteúdo utilizando predefinições de Serviços de Comunicação v3, cria uma **Transformação** e submete um **Job** que utiliza uma destas predefinições: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) ou **AudioAnalyzerPreset**. Para um tutorial que demonstre como utilizar **o VideoAnalyzerPreset,** consulte [vídeos da Análise com a Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Quando utilizar predefinições de um Analisador de Vídeo ou Áudio, utilize o portal Azure para definir a sua conta para ter 10 Unidades Reservadas a Mídia S3, embora tal não seja necessário. Pode utilizar S1 ou S2 para pré-sintonias áudio. Para obter mais informações, veja [Dimensionar o processamento de multimédia](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um lembrete importante, deve cumprir todas as leis aplicáveis na sua utilização do Video Indexer, e não pode utilizar o Video Indexer ou qualquer outro serviço Azure de forma a violar os direitos dos outros ou pode ser prejudicial para os outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço de Indexer de Vídeo para processamento e armazenamento, deve ter todos os direitos adequados, incluindo todos os consentimentos adequados, do(s) individual(s) no vídeo. Para conhecer a conformidade, privacidade e segurança no Video Indexer, os [Termos de Serviços Cognitivos da](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)Microsoft . Para as obrigações de privacidade da Microsoft e tratamento dos seus dados, por favor reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, [os Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) ("OST") e a [adenda de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Informações adicionais sobre privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui.](../video-indexer/faq.md) Ao utilizar o Video Indexer, concorda em ficar vinculado pelos Termos dos Serviços Cognitivos, o OST, o DPA e a Declaração de Privacidade.

## <a name="built-in-presets"></a>Predefinições embutidas

Atualmente, os Serviços de Comunicação Social suportam as seguintes predefinições de analisador incorporadas:  

|**Nome predefinido**|**Cenário**|**Detalhes**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Análise de Padrão áudio|A predefinição aplica um conjunto predefinido de operações de análise baseadas em IA, incluindo a transcrição da fala. Atualmente, a predefinição suporta o processamento de conteúdos com uma única faixa áudio que contém a fala numa única língua. Pode especificar o idioma para a carga áudio na entrada utilizando o formato BCP-47 de "região de identificação linguística". As línguas apoiadas são o inglês ('en-US' e 'en-GB'), espanhol ('es-ES' e 'es-MX'), francês ('fr-FR'), italiano ('it-IT'), japonês ('ja-JP'), português ('pt'-BR'), chineses ('zh-CN'), alemães ('de-DE'), árabe ('ar-EG' e 'ar-SY'), russo ('ru-RU'), Hindi ('hi-IN') e coreano ('ko-KR').<br/><br/> Se o idioma não for especificado ou definido como nulo, a deteção automática da linguagem escolhe o primeiro idioma detetado e continua com o idioma selecionado durante a duração do ficheiro. A funcionalidade de deteção automática de idiomas suporta atualmente inglês, chinês, francês, alemão, italiano, japonês, espanhol, russo e português. Não suporta a troca dinâmica entre línguas após a deteção da primeira língua. A funcionalidade de deteção automática de linguagem funciona melhor com gravações áudio com um discurso claramente percetível. Se a deteção automática de idiomas não encontrar o idioma, a transcrição volta ao inglês.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Análise áudio Básico|Este modo executa a transcrição de discurso-a-texto e a geração de um ficheiro de legenda/legenda VTT. A saída deste modo inclui um ficheiro Insights JSON, incluindo apenas as palavras-chave, transcrição e informações de tempo. A deteção automática de idiomas e a diarização dos altifalantes não estão incluídas neste modo. A lista de línguas apoiadas está disponível [aqui](#built-in-presets)|
|[VídeoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Análise de áudio e vídeo|Extrai insights (metadados ricos) tanto de áudio como de vídeo, e produz um ficheiro de formato JSON. Pode especificar se pretende apenas extrair informações áudio ao processar um ficheiro de vídeo. Para mais informações, consulte [o vídeo da Análise.](analyze-videos-tutorial-with-api.md)|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Deteção de rostos presentes em vídeo|Descreve as definições a serem usadas ao analisar um vídeo para detetar todos os rostos presentes.|

### <a name="audioanalyzerpreset-standard-mode"></a>Modo padrão AudioAnalyzerPreset

A predefinição permite extrair várias informações áudio de um ficheiro áudio ou de vídeo.

A saída inclui um ficheiro JSON (com todas as informações) e ficheiro VTT para a transcrição áudio. Esta predefinição aceita um imóvel que especifica o idioma do ficheiro de entrada sob a forma de uma cadeia [BCP47.](https://tools.ietf.org/html/bcp47) As informações áudio incluem:

* **Transcrição áudio**: Uma transcrição das palavras faladas com os tempos. Várias línguas são suportadas.
* **Indexação do altifalante**: Um mapeamento dos altifalantes e as palavras faladas correspondentes.
* **Análise do sentimento da** fala : A produção da análise de sentimento realizada na transcrição áudio.
* **Palavras-chave**: Palavras-chave extraídas da transcrição áudio.

### <a name="audioanalyzerpreset-basic-mode"></a>Modo básico AudioAnalyzerPreset

A predefinição permite extrair várias informações áudio de um ficheiro áudio ou de vídeo.

A saída inclui um ficheiro JSON e um ficheiro VTT para a transcrição áudio. Esta predefinição aceita um imóvel que especifica o idioma do ficheiro de entrada sob a forma de uma cadeia [BCP47.](https://tools.ietf.org/html/bcp47) A saída inclui:

* **Transcrição áudio**: Uma transcrição das palavras faladas com os tempos. Vários idiomas são suportados, mas a deteção automática de linguagem e a diarização dos altifalantes não estão incluídas.
* **Palavras-chave**: Palavras-chave extraídas da transcrição áudio.

### <a name="videoanalyzerpreset"></a>VídeoAnalyzerPreset

A predefinição permite extrair várias informações áudio e vídeo de um ficheiro de vídeo. A saída inclui um ficheiro JSON (com todas as informações), um ficheiro VTT para a transcrição de vídeo e uma coleção de miniaturas. Esta predefinição também aceita uma corda [BCP47](https://tools.ietf.org/html/bcp47) (representando a linguagem do vídeo) como um imóvel. As informações em vídeo incluem todas as informações áudio acima mencionadas e os seguintes itens adicionais:

* **Rastreio facial**: O tempo durante o qual os rostos estão presentes no vídeo. Cada rosto tem uma identificação facial e uma coleção correspondente de miniaturas.
* **Texto visual**: O texto detetado através do reconhecimento de caracteres óticos. O texto é carimbado no tempo e também utilizado para extrair palavras-chave (além da transcrição áudio).
* **Quadros-chave**: Uma coleção de quadros-chave extraídos do vídeo.
* **Moderação de conteúdo visual**: A parte dos vídeos sinalizados como adultos ou picantes na natureza.
* **Anotação**: Resultado da anotação dos vídeos com base num modelo de objeto pré-definido

## <a name="insightsjson-elements"></a>insights.jsem elementos

A saída inclui um ficheiro JSON (insights.jsligado) com todas as informações encontradas no vídeo ou áudio. O JSON pode conter os seguintes elementos:

### <a name="transcript"></a>transcrição

|Nome|Descrição|
|---|---|
|ID|A identificação da linha.|
|texto|A transcrição em si.|
|language|A linguagem da transcrição. Destina-se a apoiar a transcrição onde cada linha pode ter uma língua diferente.|
|casos|Uma lista de intervalos de tempo onde esta linha apareceu. Se o caso for transcrição, terá apenas 1 instância.|

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
|ID|A identificação da linha OCR.|
|texto|O texto do OCR.|
|confiança|A confiança de reconhecimento.|
|language|A linguagem OCR.|
|casos|Uma lista de intervalos de tempo onde este OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

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
|name|O nome do rosto. Pode ser 'Unknown #0', uma celebridade identificada, ou uma pessoa treinada por um cliente.|
|confiança|A confiança de identificação facial.|
|descrição|Uma descrição da celebridade. |
|miniaturaId|A identificação da miniatura do rosto.|
|conhecidoPersonId|A ID interna (se for uma pessoa conhecida).|
|referenceId|O Bing ID (se for uma celebridade Bing).|
|tipo de referência|Atualmente apenas Bing.|
|título|O título (se for uma celebridade — por exemplo, "CEO da Microsoft").|
|imageUrl|A URL de imagem, se for uma celebridade.|
|casos|Casos em que o rosto apareceu no intervalo de tempo. Cada caso também tem uma miniatura. |

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
|keyFrames|Uma lista de quadros chave dentro do tiro (cada um tem um ID e uma lista de intervalos de tempo de instâncias). As instâncias de quadros-chave têm um campo miniaturaid com o ID da miniatura do KeyFrame.|
|casos|Uma lista de intervalos de tempo deste tiro (os tiros têm apenas 1 instância).|

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
|Conta de Correspondência|Número de correspondências no vídeo.|
|WordCount|O número de palavras por orador.|
|SpeakerNumberOfFragments|A quantidade de fragmentos que o altifalante tem num vídeo.|
|OradorLongestMonolog|O maior monótono do orador. Se o orador tiver silêncios dentro do monótono, está incluído. O silêncio no início e no fim do monóloga é removido.|
|SpeakerTalkToListenRatio|O cálculo baseia-se no tempo gasto no monótono do orador (sem o silêncio no meio) dividido pelo tempo total do vídeo. O tempo é arredondado para o terceiro ponto decimal.|


### <a name="sentiments"></a>sentimentos

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

### <a name="labels"></a>rótulos

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

### <a name="keywords"></a>keywords

|Nome|Descrição|
|---|---|
|ID|A identificação da palavra-chave.|
|texto|O texto da palavra-chave.|
|confiança|A confiança de reconhecimento da palavra-chave.|
|language|A linguagem da palavra-chave (quando traduzida).|
|casos|Uma lista de intervalos de tempo onde esta palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

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

O bloco visualContentModeration contém intervalos de tempo que o Video Indexer descobriu potencialmente ter conteúdo adulto. Se o visualContentModeration estiver vazio, não há nenhum conteúdo adulto que tenha sido identificado.

Os vídeos que contêm conteúdo adulto ou picante podem estar disponíveis apenas para visualização privada. Os utilizadores podem submeter um pedido de revisão humana do conteúdo, caso em que o `IsAdult` atributo conterá o resultado da revisão humana.

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
## <a name="next-steps"></a>Próximos passos

[Tutorial: Analyze videos with Azure Media Services](analyze-videos-tutorial-with-api.md) (Tutorial: analisar vídeos com os Serviços de Multimédia do Azure)