---
title: Moderação de texto-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a moderação de texto para possíveis textos indesejados, dados pessoais e listas personalizadas de termos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538832"
---
# <a name="learn-text-moderation-concepts"></a>Aprenda conceitos de moderação de texto

Use os recursos de moderação de texto assistido por máquina Content Moderator e [revisão humana](Review-Tool-User-Guide/human-in-the-loop.md) para conteúdo de texto moderado.

Você pode bloquear, aprovar ou examinar o conteúdo com base em suas políticas e limites. Use-o para aumentar a moderação humana de ambientes em que parceiros, funcionários e consumidores geram conteúdo de texto. Isso inclui salas de bate-papo, quadros de discussão, chatbots, catálogos de comércio eletrônico e documentos. 

A resposta do serviço inclui as seguintes informações:

- Profanação: correspondência baseada em termos com a lista interna de termos obscenas em várias linguagens
- Classificação: classificação assistida por computador em três categorias
- Dados pessoais
- Texto corrigido automaticamente
- Texto original
- Linguagem

## <a name="profanity"></a>Profanidade

Se a API detectar quaisquer termos impróprios em qualquer um dos [idiomas com suporte](Text-Moderation-API-Languages.md), esses termos serão incluídos na resposta. A resposta também contém seu local (`Index`) no texto original. O `ListId` no exemplo JSON a seguir refere-se aos termos encontrados em [listas de termos personalizados](try-terms-list-api.md) , se disponível.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Para o parâmetro de **idioma** , atribua `eng` ou deixe-o vazio para ver a resposta de **classificação** assistida por computador (recurso de visualização). **Esse recurso dá suporte apenas ao inglês**.
>
> Para a detecção de **termos de profanação** , use o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte listados neste artigo ou deixe-o vazio.

## <a name="classification"></a>Classificação

O recurso de classificação de **texto** auxiliada por máquina do Content moderator dá suporte **apenas ao inglês**e ajuda a detectar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser avaliado como inadequado, dependendo do contexto. Ele transmite a probabilidade de cada categoria e pode recomendar uma revisão humana. O recurso usa um modelo treinado para identificar possíveis linguagens abusiva, derrogatório ou discriminais. Isso inclui palavras gíriasdas, abreviadas, ofensivas e intencionalmente incorretas para análise. 

A extração a seguir na extração JSON mostra um exemplo de saída:

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

- `Category1` se refere a uma possível presença de linguagem que pode ser considerada sexualmente explícita ou adulto em determinadas situações.
- `Category2` se refere a uma possível presença de linguagem que pode ser considerada sexualmente sugerida ou amadurece em determinadas situações.
- `Category3` refere-se à possível presença de linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score` está entre 0 e 1. Quanto maior a pontuação, mais alto o modelo está prevendo que a categoria pode ser aplicável. Esse recurso depende de um modelo estatístico em vez de resultados codificados manualmente. É recomendável testar com seu próprio conteúdo para determinar como cada categoria se alinha aos seus requisitos.
- `ReviewRecommended` é true ou false, dependendo dos limites de Pontuação internos. Os clientes devem avaliar se esse valor deve ser usado ou decidir sobre limites personalizados com base em suas políticas de conteúdo.

## <a name="personal-data"></a>Dados pessoais

O recurso de dados pessoais detecta a presença potencial dessas informações:

- Endereço de e-mail
- Endereço de correspondência dos EUA
- Endereço IP
- Número de telefone dos EUA
- Número de telefone do Reino Unido
- Número do seguro social (SSN)

O exemplo a seguir mostra uma resposta de exemplo:

```json
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
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
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
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Correção automática

Suponha que o texto de entrada seja (o ' lzay ' e o ' f0x ' são intencionais):

    The qu!ck brown f0x jumps over the lzay dog.

Se você solicitar a correção automática, a resposta conterá a versão corrigida do texto:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criando e gerenciando suas listas personalizadas de termos

Embora a lista global de termos padrão funcione bem para a maioria dos casos, talvez você queira fazer uma triagem em relação aos termos específicos às suas necessidades de negócios. Por exemplo, talvez você queira filtrar quaisquer nomes de marca de concorrência de postagens por usuários.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

O exemplo a seguir mostra a ID da lista correspondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

O Content Moderator fornece uma [API de lista de termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para gerenciar listas de termos personalizados. Comece com o [termo listar o console de API](try-terms-list-api.md) e use os exemplos de código da API REST. Confira também o [termo listas de início rápido do .net](term-lists-quickstart-dotnet.md) se você estiver familiarizado com C#o Visual Studio e o.

## <a name="next-steps"></a>Passos seguintes

Teste o [console da API de moderação de texto](try-text-api.md) e use os exemplos de código da API REST. Confira também a seção de moderação de texto do [início rápido do SDK do .net](dotnet-sdk-quickstart.md) se você estiver C#familiarizado com o Visual Studio e o.
