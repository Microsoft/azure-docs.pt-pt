---
title: Texto moderado com a API de moderação de texto - Content Moderator
titlesuffix: Azure Cognitive Services
description: Testar a moderação de texto com a API de moderação de texto na consola do online.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 21209548d1cfe7b6eebb1757e817a12c797e78a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688834"
---
# <a name="moderate-text-from-the-api-console"></a>Texto moderado, a partir da consola de API

Utilize o [API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Azure Content Moderator para analisar o conteúdo de texto relativamente a profanidades e compará-lo em relação a listas personalizadas e partilhadas.

## <a name="get-your-api-key"></a>Obtenha a chave de API

Antes de pode testar a API na consola do online, terá a chave de subscrição. Isto está localizado no **configurações** separador a **Ocp-Apim-Subscription-Key** caixa. Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegue para a referência de API

Vá para o [referência da API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  O **texto - ecrã** é aberta a página.

## <a name="open-the-api-console"></a>Abra a consola de API

Para **consola de teste de API aberta**, selecione a região que melhor descreve sua localização. 

  ![Texto - seleção de região da página de ecrã](images/test-drive-region.png)

  O **texto - ecrã** é aberta a consola de API.

## <a name="select-the-inputs"></a>Selecione as entradas

### <a name="parameters"></a>Parâmetros

Selecione os parâmetros de consulta que pretende utilizar no seu texto na tela. Neste exemplo, utilize o valor predefinido para **linguagem**. Pode também deixá-lo em branco porque a operação irá detetar automaticamente o idioma provavelmente como parte de sua execução.

> [!NOTE]
> Para o **linguagem** parâmetro, atribuir `eng` ou deixe vazio para ver o assistida **classificação** resposta (funcionalidade de pré-visualização). **Esta funcionalidade só suporta o inglês**.
>
> Para **termos de linguagem inapropriada** deteção, utilize o [código ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte demonstrados neste artigo ou deixe-o vazio.

Para **autocorreção**, **PII**, e **classificar (pré-visualização)** , selecione **verdadeiro**. Deixe o **ListId** campo vazio.

  ![Texto - parâmetros de consulta da consola de ecrã](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo de conteúdo

Para **Content-Type**, selecione o tipo de conteúdo que pretende ecrã. Neste exemplo, utilize a predefinição **text/plain** tipo de conteúdo. Na **Ocp-Apim-Subscription-Key** , introduza a chave de subscrição.

### <a name="sample-text-to-scan"></a>Texto de exemplo para analisar

Na **corpo do pedido** , introduza algum texto. O exemplo seguinte mostra um erro de digitação intencional no texto.

> [!NOTE]
> O número de segurança social inválidos no texto de exemplo a seguir é intencional. O objetivo é transmitir a entrada de exemplo e formato de saída.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analisar a resposta

A seguinte resposta mostra as várias informações da API. Ela contém potencial linguagem inapropriada, os dados pessoais, classificação (pré-visualização) e a versão corrigidas automaticamente.

> [!NOTE]
> A funcionalidade de "Classificação" assistida está em pré-visualização e suporta apenas o inglês.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
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
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
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

Para obter uma explicação detalhada de todas as secções na resposta JSON, consulte a [moderação de texto](text-moderation-api.md) guia conceitual.

## <a name="next-steps"></a>Passos Seguintes

Utilizar a API REST no seu código ou começar com o [guia de introdução de .NET de moderação de texto](text-moderation-quickstart-dotnet.md) para integrar com a sua aplicação.
