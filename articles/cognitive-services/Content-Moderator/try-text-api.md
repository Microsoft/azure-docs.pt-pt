---
title: Texto moderado usando a API de moderação de texto-Content Moderator
titleSuffix: Azure Cognitive Services
description: Teste a moderação de texto usando a API de moderação de texto no console online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538829"
---
# <a name="moderate-text-from-the-api-console"></a>Texto moderado do console de API

Use a [API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Content moderator do Azure para verificar seu conteúdo de texto em busca de profanação e compará-lo em listas personalizadas e compartilhadas.

## <a name="get-your-api-key"></a>Obter sua chave de API

Antes de poder testar a API no console online, você precisa da sua chave de assinatura. Isso está localizado na guia **configurações** , na caixa **OCP-APIM-Subscription-Key** . Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegue até a referência de API

Vá para a [referência da API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  A página da **tela de texto** é aberta.

## <a name="open-the-api-console"></a>Abrir o console de API

Para **abrir o console de teste de API**, selecione a região que melhor descreve seu local. 

  ![Seleção de região de página da tela de texto](images/test-drive-region.png)

  O console da API de **tela de texto** é aberto.

## <a name="select-the-inputs"></a>Selecionar as entradas

### <a name="parameters"></a>Parâmetros

Selecione os parâmetros de consulta que você deseja usar na tela de texto. Para este exemplo, use o valor padrão para **Language**. Você também pode deixá-lo em branco porque a operação detectará automaticamente o idioma provável como parte de sua execução.

> [!NOTE]
> Para o parâmetro de **idioma** , atribua `eng` ou deixe-o vazio para ver a resposta de **classificação** assistida por computador (recurso de visualização). **Esse recurso dá suporte apenas ao inglês**.
>
> Para a detecção de **termos de profanação** , use o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte listados neste artigo ou deixe-o vazio.

Para **AutoCorreção**, **PII**e **classificar (visualização)** , selecione **verdadeiro**. Deixe o campo **ListId** vazio.

  ![Parâmetros de consulta do console de tela de texto](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo de conteúdo

Para **Content-Type**, selecione o tipo de conteúdo que você deseja para a tela. Para este exemplo, use o tipo de conteúdo padrão **/texto sem formatação** . Na caixa **OCP-APIM-Subscription-Key** , insira sua chave de assinatura.

### <a name="sample-text-to-scan"></a>Texto de exemplo para verificação

Na caixa **corpo da solicitação** , insira algum texto. O exemplo a seguir mostra uma grafia intencional no texto.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analisar a resposta

A resposta a seguir mostra as várias informações da API. Ele contém possíveis obscenidades, dados pessoais, classificação (visualização) e a versão corrigida automaticamente.

> [!NOTE]
> O recurso ' classificação ' assistido por computador está em visualização e dá suporte apenas ao inglês.

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

Para obter uma explicação detalhada de todas as seções na resposta JSON, consulte o guia conceitual de [moderação de texto](text-moderation-api.md) .

## <a name="next-steps"></a>Passos seguintes

Use a API REST em seu código ou siga o [início rápido do SDK do .net](dotnet-sdk-quickstart.md) para integrar com seu aplicativo.
