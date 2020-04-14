---
title: Moderação de Texto - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize moderação de texto para possível texto indesejado, dados pessoais e listas personalizadas de termos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274015"
---
# <a name="learn-text-moderation-concepts"></a>Aprenda conceitos de moderação de texto

Utilize a moderação de texto assistida por máquina sanções e capacidades de [revisão humana](Review-Tool-User-Guide/human-in-the-loop.md) do Content Moderator para moderar o conteúdo de texto.

Pode bloquear, aprovar ou rever os conteúdos com base nas suas políticas e limiares. Use-o para aumentar a moderação humana de ambientes onde parceiros, colaboradores e consumidores geram conteúdo de texto. Estes incluem salas de chat, áreas de debate, chatbots, catálogos de comércio eletrónico e documentos. 

A resposta do serviço inclui as seguintes informações:

- Profanidade: correspondência baseada em termo com lista incorporada de termos profanos em várias línguas
- Classificação: classificação assistida por máquinaem três categorias
- Dados pessoais
- Texto corrigido automaticamente
- Texto original
- Idioma

## <a name="profanity"></a>Palavras ofensivas

Se a API detetar quaisquer termos profanos em qualquer uma das [línguas apoiadas,](Text-Moderation-API-Languages.md)esses termos estão incluídos na resposta. A resposta também contém`Index`a sua localização no texto original. A `ListId` amostra seguinte JSON refere-se aos termos encontrados nas [listas de termo personalizadas,](try-terms-list-api.md) se disponível.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Para o parâmetro **linguístico,** atribua `eng` ou deixe vazio para ver a resposta de **classificação** assistida pela máquina (função de pré-visualização). **Esta funcionalidade suporta apenas inglês.**
>
> Para deteção de **termos de profanação,** utilize o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) das línguas suportadas enumeradas neste artigo, ou deixe-o vazio.

## <a name="classification"></a>Classificação

A funcionalidade de **classificação** de texto assistida por máquinas do Moderador de Conteúdo suporta **apenas o inglês**e ajuda a detetar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser avaliado como inadequado em função do contexto. Transmite a probabilidade de cada categoria e pode recomendar uma revisão humana. A funcionalidade utiliza um modelo treinado para identificar uma possível linguagem abusiva, depreciativa ou discriminatória. Isto inclui gíria, palavras abreviadas, palavras ofensivas e intencionalmente mal escritas para revisão. 

O seguinte extrato no extrato JSON mostra uma saída exemplo:

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
    }

### <a name="explanation"></a>Explicação

- `Category1`refere-se à presença potencial de linguagem que pode ser considerada sexualmente explícita ou adulta em determinadas situações.
- `Category2`refere-se à presença potencial de linguagem que pode ser considerada sexualmente sugestiva ou madura em determinadas situações.
- `Category3`refere-se à potencial presença de linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score`é entre 0 e 1. Quanto maior for a pontuação, maior é o modelo que prevê que a categoria pode ser aplicável. Esta funcionalidade baseia-se num modelo estatístico em vez de resultados codificados manualmente. Recomendamos testar com o seu próprio conteúdo para determinar como cada categoria se alinha com os seus requisitos.
- `ReviewRecommended`é verdadeiro ou falso dependendo dos limiares de pontuação internos. Os clientes devem avaliar se utilizam este valor ou decidem sobre os limiares personalizados com base nas suas políticas de conteúdo.

## <a name="personal-data"></a>Dados pessoais

A funcionalidade de dados pessoais deteta a presença potencial desta informação:

- Endereço de e-mail
- Endereço de correio dos EUA
- Endereço IP
- Número de telefone dos EUA

O exemplo seguinte mostra uma resposta da amostra:

```json
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
}
```

## <a name="auto-correction"></a>Correção automática

Suponha que o texto de entrada é (o 'lzay' e o 'f0x' são intencionais):

    The qu!ck brown f0x jumps over the lzay dog.

Se pedir a correção automática, a resposta contém a versão corrigida do texto:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criar e gerir as suas listas personalizadas de termos

Embora a lista de termos global funcione muito bem para a maioria dos casos, pode querer ser exibido contra termos específicos das necessidades do seu negócio. Por exemplo, pode querer filtrar quaisquer marcas competitivas a partir de publicações por utilizadores.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

O exemplo seguinte mostra o ID da lista correspondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

O Moderador de Conteúdo fornece uma Lista de [Prazos API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para gerir listas de prazos personalizados. Comece com as [Listas de Prazo Sapi Console](try-terms-list-api.md) e utilize as amostras de código REST API. Consulte também as [Listas de Prazos .NET quickstart](term-lists-quickstart-dotnet.md) se estiver familiarizado com visual studio e C#.

## <a name="next-steps"></a>Passos seguintes

Teste a [consola API de moderação](try-text-api.md) de texto e utilize as amostras de código REST API. Consulte também a secção de moderação de Texto do [quickstart .NET SDK](dotnet-sdk-quickstart.md) se estiver familiarizado com o Visual Studio e o C#.
