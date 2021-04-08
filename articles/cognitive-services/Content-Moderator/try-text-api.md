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
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002005"
---
# <a name="moderate-text-from-the-api-console"></a>Texto moderado da consola API

Utilize a [API de Moderação de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Moderador de Conteúdo Azure para digitalizar o seu conteúdo de texto para profanação e compará-lo com listas personalizadas e partilhadas.

## <a name="get-your-api-key"></a>Obtenha a sua chave API

Antes de poder testar a API na consola online, precisa da sua chave de subscrição. Isto está localizado no **separador Definições,** na caixa **Ocp-Apim-Subscription-Key.** Para obter mais informações, veja [Descrição geral](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegue até à referência da API

Aceda à referência da [API de Moderação de Texto.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 

  O **Texto - Página do ecrã** abre.

## <a name="open-the-api-console"></a>Abra a consola API

Para **a consola de testes API Aberta,** selecione a região que mais descreve a sua localização. 

  ![Texto - Seleção da região da página do ecrã](images/test-drive-region.png)

  A consola **Text - Screen** API abre.

## <a name="select-the-inputs"></a>Selecione as entradas

### <a name="parameters"></a>Parâmetros

Selecione os parâmetros de consulta que pretende utilizar no seu ecrã de texto. Para este exemplo, utilize o valor predefinido para **a linguagem**. Também pode deixá-lo em branco porque a operação irá detetar automaticamente o idioma provável como parte da sua execução.

> [!NOTE]
> Para o parâmetro do **idioma,** `eng` atribua-o ou deixe-o vazio para ver a resposta de **classificação** assistida por máquina (função de pré-visualização). **Esta funcionalidade suporta apenas inglês.**
>
> Para deteção **de termos de profanação,** utilize o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) das línguas suportadas listadas neste artigo ou deixe-o vazio.

Para **corrigir automaticamente,** **PII,** e **classificar (pré-visualização)**, selecione **true**. Deixe o campo **ListId** vazio.

  ![Texto - Parâmetros de consulta da consola de ecrã](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo do conteúdo

Para **o Tipo de Conteúdo,** selecione o tipo de conteúdo que pretende selecionar. Para este exemplo, utilize o tipo de **texto/conteúdo simples predefinido.** Na caixa **Ocp-Apim-Subscription-Key,** insira a sua chave de subscrição.

### <a name="sample-text-to-scan"></a>Texto de amostra para digitalizar

Na caixa **de corpo Request,** introduza algum texto. O exemplo que se segue mostra uma tipografia intencional no texto.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analisar a resposta

A resposta a seguir mostra as várias informações da API. Contém potencial profanação, dados pessoais, classificação (pré-visualização) e a versão corrigida automaticamente.

> [!NOTE]
> A função 'Classificação assistida por máquina' está em pré-visualização e suporta apenas inglês.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Para obter uma explicação detalhada de todas as secções na resposta JSON, consulte o guia conceptual [de moderação](text-moderation-api.md) do texto.

## <a name="next-steps"></a>Passos seguintes

Utilize a API REST no seu código ou siga o [quickstart .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) para integrar-se com a sua aplicação.