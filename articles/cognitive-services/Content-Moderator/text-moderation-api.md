---
title: Moderação de texto - o Content Moderator
description: Utilize a moderação de texto para o possível indesejável texto, PII, e personalizada apresenta uma lista dos termos.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5a1007f2408b48c96f5eeaf585b94c8caa7ceb45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607131"
---
# <a name="learn-text-moderation-concepts"></a>Aprenda os conceitos de moderação de texto

Utilize a moderação de texto de assistida do Content Moderator e [revisão humana](Review-Tool-User-Guide/human-in-the-loop.md) capacidades moderar conteúdo de texto.

O bloquear, aprova ou rever o conteúdo com base nas suas políticas e limiares. Usá-lo para incrementar humana moderação de ambientes em que os funcionários, parceiros e os consumidores geram conteúdo de texto. Estes incluem salas de chat, quadros de discussão, chatbots, catálogos de comércio eletrônico e documentos. 

A resposta do serviço inclui as seguintes informações:

- Linguagem inapropriada: com base no termo correspondência com uma lista interna de impróprias termos em várias linguagens
- Classificação: assistida classificação em três categorias
- Dados pessoais
- Texto corrigidas automaticamente
- Texto original
- Idioma

## <a name="profanity"></a>Linguagem inapropriada

Se a API detetar quaisquer termos impróprias em qualquer uma da [idiomas suportados](Text-Moderation-API-Languages.md), esses termos estão incluídos na resposta. A resposta também contém a respetiva localização (`Index`) no texto original. O `ListId` no exemplo a seguir JSON refere-se termos encontrados no [listas de termos personalizado](try-terms-list-api.md) se estiver disponível.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Para o **linguagem** parâmetro, atribuir `eng` ou deixe vazio para ver o assistida **classificação** resposta (funcionalidade de pré-visualização). **Esta funcionalidade só suporta o inglês**.
>
> Para **termos de linguagem inapropriada** deteção, utilize o [código ISO 639 3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte demonstrados neste artigo ou deixe-o vazio.

## <a name="classification"></a>Classificação

O Content Moderator da assistida **funcionalidade de classificação de texto** suporta **inglês apenas**, e ajuda a detetar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser avaliado como inadequado consoante o contexto. Ele transmite a probabilidade de cada categoria e poderá recomendar uma revisão humana. A funcionalidade utiliza um modelo preparado para identificar possíveis linguagem abusiva, depreciativo ou discriminatory. Isto inclui gíria, abreviadas palavras, palavras ofensivas e intencionalmente com erros ortográficos para revisão. 

O seguinte extrato no extrato de JSON mostra uma saída de exemplo:

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

- `Category1` refere-se a presença de potencial de idioma que pode ser considerada sexualmente explícita ou para adultos em determinadas situações.
- `Category2` refere-se a presença de potencial de idioma que pode ser considerada sexualmente suggestive ou madura e em determinadas situações.
- `Category3` refere-se a presença de potencial de idioma que pode ser considerada ofensiva em determinadas situações.
- `Score` é entre 0 e 1. Quanto maior for a pontuação, maior será o modelo é a previsão que a categoria pode ser aplicável. Esta funcionalidade baseia-se num modelo estatístico em vez de resultados codificados manualmente. Recomendamos que teste com o seu próprio conteúdo para determinar a forma como cada categoria alinha os seus requisitos.
- `ReviewRecommended` é true ou false dependendo da pontuação interna limiares. Os clientes devem avaliar se pretende utilizar este valor ou opte por utilizar limiares personalizados com base em suas diretivas de conteúdo.

## <a name="personal-data"></a>Dados pessoais

A funcionalidade PII detecta a presença potencial estas informações:

- Endereço de e-mail
- Endereço de correio dos EUA
- Endereço IP
- Número de telefone dos EUA
- Número de telefone do Reino Unido
- Número da Previdência Social (SSN)

O exemplo seguinte mostra uma resposta de exemplo:

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
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
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
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Correção automática

Suponha que seja a introdução de texto (o 'lzay' e 'f0x' são intencionais):

    The qu!ck brown f0x jumps over the lzay dog.

Se solicitar a correção automática, a resposta contém a versão corrigida do texto:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criar e gerir as suas listas personalizadas dos termos

Embora o padrão, a lista global de termos funciona muito bem para a maioria dos casos, pode querer ecrã contra termos que são específicos para suas necessidades empresariais. Por exemplo, pode querer filtrar qualquer competitivos os nomes de postagens por utilizadores.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.
>

O exemplo seguinte mostra o ID de lista correspondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

O Content Moderator fornece uma [API de lista do termo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para o gerenciamento de termo personalizado apresenta uma lista. Começar com o [consola de API de lista termo](try-terms-list-api.md) e utilize os exemplos de código da REST API. Verifique também a [guia de introdução do .NET apresenta uma lista de termo](term-lists-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e c#.

## <a name="next-steps"></a>Passos Seguintes

Faça o test drive da [consola de API de moderação de texto](try-text-api.md) e utilize os exemplos de código da REST API. Verifique também a [guia de introdução de .NET de moderação de texto](text-moderation-quickstart-dotnet.md) se estiver familiarizado com o Visual Studio e c#.
