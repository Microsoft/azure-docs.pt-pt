---
title: Moderação de texto - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize moderação de texto para possíveis textos indesejados, dados pessoais e listas de termos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912910"
---
# <a name="learn-text-moderation-concepts"></a>Aprenda conceitos de moderação de texto

Utilize os modelos de moderação de texto do Moderador de Conteúdo para analisar o conteúdo de texto.

Pode bloquear, aprovar ou rever o conteúdo com base nas suas políticas e limiares (ver [revisões, fluxos de trabalho e empregos](./review-api.md) para aprender a configurar revisões humanas). Utilize os modelos de moderação de texto para aumentar a moderação humana de ambientes onde parceiros, colaboradores e consumidores geram conteúdo de texto. Estes incluem salas de chat, áreas de debate, chatbots, catálogos de comércio eletrónico e documentos.

A resposta do serviço inclui as seguintes informações:

- Profanidade: correspondência baseada em termos baseados com lista incorporada de termos profanos em várias línguas
- Classificação: classificação assistida por máquinas em três categorias
- Dados pessoais
- Texto corrigido automaticamente
- Texto original
- Linguagem

## <a name="profanity"></a>Palavras ofensivas

Se a API detetar quaisquer termos profanos em qualquer uma das [línguas suportadas,](./language-support.md)esses termos estão incluídos na resposta. A resposta também contém a sua localização `Index` () no texto original. Na `ListId` amostra seguinte, jSON refere-se aos termos encontrados em [listas de prazos personalizados,](try-terms-list-api.md) se disponível.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> Para o parâmetro do **idioma,** `eng` atribua-o ou deixe-o vazio para ver a resposta de **classificação** assistida por máquina (função de pré-visualização). **Esta funcionalidade suporta apenas inglês.**
>
> Para deteção **de termos de profanação,** utilize o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) das línguas suportadas listadas neste artigo ou deixe-o vazio.

## <a name="classification"></a>Classificação

A funcionalidade de **classificação** de texto assistida por máquinas do Moderador suporta **apenas o inglês** e ajuda a detetar conteúdo potencialmente não-domado. O conteúdo sinalizado pode ser avaliado como inadequado em função do contexto. Transmite a probabilidade de cada categoria e pode recomendar uma revisão humana. A funcionalidade utiliza um modelo treinado para identificar possíveis linguagens abusivas, depreciativas ou discriminatórias. Isto inclui gíria, palavras abreviadas, ofensivas e intencionalmente escritas palavras para revisão. 

O seguinte extrato no extrato JSON mostra uma saída de exemplo:

```json
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
```

### <a name="explanation"></a>Explicação

- `Category1` refere-se à presença potencial de linguagem que pode ser considerada sexualmente explícita ou adulta em determinadas situações.
- `Category2` refere-se à presença potencial de linguagem que pode ser considerada sexualmente sugestiva ou madura em determinadas situações.
- `Category3` refere-se à potencial presença de linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score` é entre 0 e 1. Quanto maior for a pontuação, maior é a previsão do modelo de que a categoria pode ser aplicável. Esta funcionalidade baseia-se num modelo estatístico e não em resultados codificados manualmente. Recomendamos testar com o seu próprio conteúdo para determinar como cada categoria se alinha com os seus requisitos.
- `ReviewRecommended` é verdadeiro ou falso dependendo dos limiares de pontuação interna. Os clientes devem avaliar se utilizam este valor ou se decidem sobre limiares personalizados com base nas suas políticas de conteúdo.

## <a name="personal-data"></a>Dados pessoais

A funcionalidade de dados pessoais deteta a potencial presença desta informação:

- Endereço de e-mail
- Endereço de correio dos EUA
- Endereço IP
- Número de telefone dos EUA

O exemplo a seguir mostra uma resposta da amostra:

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

Suponha que o texto de entrada é (o 'lzay' e 'f0x' são intencionais):

> O qu!ck brown f0x salta sobre o cão lzay.

Se pedir uma correção automática, a resposta contém a versão corrigida do texto:

> A raposa castanha rápida salta por cima do cão preguiçoso.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criar e gerir as suas listas de termos personalizados

Embora o padrão, a lista global de termos funcione muito bem para a maioria dos casos, você pode querer ser exibido contra termos específicos das necessidades do seu negócio. Por exemplo, é melhor filtrar quaisquer marcas competitivas de publicações por parte dos utilizadores.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

O exemplo a seguir mostra o ID da lista correspondente:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

O Moderador de Conteúdo fornece uma [API de Lista de Prazos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para gerir listas de prazos personalizados. Comece com a [Consola API Listas de Prazos](try-terms-list-api.md) e utilize as amostras de código REST API. Confira também as [Listas de Prazos .NET quickstart](term-lists-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e C#.

## <a name="next-steps"></a>Passos seguintes

Teste as APIs com a [consola API de moderação de texto](try-text-api.md). Consulte também [revisões, fluxos de trabalho e empregos](./review-api.md) para aprender a configurar críticas humanas.