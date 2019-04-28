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
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995721"
---
# <a name="moderate-text-from-the-api-console"></a>Texto moderado, a partir da consola de API

Utilize o [API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Azure Content Moderator para analisar o conteúdo de texto. A operação analisa os seus conteúdos relativamente a profanidades e compara o conteúdo em relação a listas de bloqueios personalizadas e partilhados.


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

Para **autocorreção**, **PII**, e **classificar (pré-visualização)**, selecione **verdadeiro**. Deixe o **ListId** campo vazio.

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

### <a name="text-classification-feature"></a>Funcionalidade de classificação de texto

No exemplo seguinte, verá de resposta de classificação de texto assistida do Content Moderator. Ajuda a detetar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser considerado como inadequado consoante o contexto. Para além de transmitir a probabilidade de cada categoria, ele poderá recomendar uma revisão de segurança do conteúdo. A funcionalidade utiliza um modelo preparado para identificar possíveis linguagem abusiva, depreciativo ou discriminatory. Isto inclui gíria, abreviadas palavras, palavras ofensivas e intencionalmente com erros ortográficos para revisão. 

#### <a name="explanation"></a>Explicação

- `Category1` representa a presença de potencial de idioma que pode ser considerada sexualmente explícita ou para adultos em determinadas situações.
- `Category2` representa a presença de potencial de idioma que pode ser considerada sexualmente suggestive ou madura e em determinadas situações.
- `Category3` representa a presença de potencial de idioma que pode ser considerada ofensiva em determinadas situações.
- `Score` é entre 0 e 1. Quanto maior for a pontuação, maior será o modelo é a previsão que a categoria pode ser aplicável. Esta pré-visualização depende de um modelo estatístico em vez de resultados codificados manualmente. Recomendamos que teste com o seu próprio conteúdo para determinar a forma como cada categoria alinha os seus requisitos.
- `ReviewRecommended` é true ou false dependendo da pontuação interna limiares. Os clientes devem avaliar se pretende utilizar este valor ou opte por utilizar limiares personalizados com base em suas diretivas de conteúdo.

### <a name="analyze-the-response"></a>Analisar a resposta
A seguinte resposta mostra as várias informações da API. Ela contém potencial linguagem inapropriada, PII, classificação (pré-visualização) e a versão corrigidas automaticamente.

> [!NOTE]
> A funcionalidade de "Classificação" assistida está em pré-visualização e suporta apenas o inglês.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Para obter uma explicação detalhada de todas as secções na resposta JSON, consulte a [descrição geral da API de moderação de texto](text-moderation-api.md).

## <a name="next-steps"></a>Passos Seguintes

Utilizar a API REST no seu código ou começar com o [guia de introdução de .NET de moderação de texto](text-moderation-quickstart-dotnet.md) para integrar com a sua aplicação.
