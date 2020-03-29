---
title: Texto moderado utilizando a API de Moderação de Texto - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Moderação de texto de test-drive utilizando a API de moderação de texto na consola online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538829"
---
# <a name="moderate-text-from-the-api-console"></a>Texto moderado da consola API

Utilize a API de [moderação](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) de texto em Moderador de Conteúdo Azure para digitalizar o seu conteúdo de texto para profanação e compará-lo com listas personalizadas e partilhadas.

## <a name="get-your-api-key"></a>Obtenha a sua chave API

Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no separador **Definições,** na caixa **De-Assinatura-Chave Ocp-Apim..** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegue para a referência da API

Vá à referência da API de [moderação](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)de texto . 

  A página **texto - tela** abre.

## <a name="open-the-api-console"></a>Abra a consola API

Para **a consola de teste API aberta,** selecione a região que mais descreve a sua localização. 

  ![Texto - Seleção da região da página de tela](images/test-drive-region.png)

  Abre-se a consola **Text - Screen** API.

## <a name="select-the-inputs"></a>Selecione as inputs

### <a name="parameters"></a>Parâmetros

Selecione os parâmetros de consulta que pretende utilizar no seu ecrã de texto. Para este exemplo, utilize o valor predefinido para **a linguagem**. Também pode deixá-lo em branco porque a operação detetará automaticamente a linguagem provável como parte da sua execução.

> [!NOTE]
> Para o parâmetro **linguístico,** atribua `eng` ou deixe vazio para ver a resposta de **classificação** assistida pela máquina (função de pré-visualização). **Esta funcionalidade suporta apenas inglês.**
>
> Para deteção de **termos de profanação,** utilize o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) das línguas suportadas enumeradas neste artigo, ou deixe-o vazio.

Para **corrigir automaticamente**, **PII,** e classificar **(pré-visualização)**, selecione **verdadeiro**. Deixe o campo **ListId** vazio.

  ![Texto - Parâmetros de consulta de consola de tela](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo do conteúdo

Para **o Tipo de Conteúdo,** selecione o tipo de conteúdo que pretende testar. Para este exemplo, utilize o tipo de **texto/conteúdo simples** predefinido. Na caixa **Ocp-Apim-Subscription-Key,** introduza a sua chave de subscrição.

### <a name="sample-text-to-scan"></a>Texto de amostra para digitalizar

Na caixa **de corpo solicitar,** insira algum texto. O exemplo que se segue mostra uma tipografia intencional no texto.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analisar a resposta

A resposta que se segue mostra as várias informações da API. Contém potencial profanação, dados pessoais, classificação (pré-visualização) e a versão corrigida automaticamente.

> [!NOTE]
> A função de 'Classificação' assistida por máquinas está na pré-visualização e suporta apenas inglês.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Para obter uma explicação detalhada de todas as secções da resposta JSON, consulte o guia conceptual da moderação do [Texto.](text-moderation-api.md)

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou siga o [quickstart .NET SDK](dotnet-sdk-quickstart.md) para integrar com a sua aplicação.
