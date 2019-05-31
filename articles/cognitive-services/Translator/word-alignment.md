---
title: Alinhamento de palavra - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Receba informações de alinhamento do word da API de texto do Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.custom: seodec18
ms.openlocfilehash: 5d48e2dd49eb3c2f449825f988618db3c6b04c0c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387293"
---
# <a name="how-to-receive-word-alignment-information"></a>Como receber informações de alinhamento do word

## <a name="receiving-word-alignment-information"></a>Receba informações de alinhamento do word
Para receber informações de alinhamento, utilize o método Translate e inclua o parâmetro opcional includeAlignment.

## <a name="alignment-information-format"></a>Formato de informações de alinhamento
Alinhamento é retornado como um valor de cadeia de caracteres no seguinte formato para todas as palavras da origem. As informações para cada palavra são separadas por um espaço, incluindo o não-separadas por espaços linguagens (scripts), como o chinês:

[[SourceTextStartIndex]:[SourceTextEndIndex]-[TgtTextStartIndex]: [TgtTextEndIndex]] *

Cadeia de alinhamento de exemplo: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Em outras palavras, a vírgula separa o início e índice final, o traço separa as linguagens e espaço separa as palavras. Uma palavra pode alinhar com zero, uma ou várias palavras no outro idioma e as palavras alinhadas podem estar não contíguos. Quando não existem informações de alinhamento estiverem disponíveis, o elemento de alinhamento estará vazio. O método não retorna nenhum erro nesse caso.

## <a name="restrictions"></a>Restrições
Alinhamento só é devolvido neste momento para um subconjunto de pares de idiomas:
* inglês para qualquer outra linguagem;
* em qualquer outra linguagem para inglês, exceto o chinês simplificado, chinês tradicional e Letão para inglês
* de japonês para coreano ou de coreano para japonês não irá receber informações de alinhamento se a frase é uma tradução em. Exemplo de uma tradução em é "Este é um teste", "Eu gosto do" e outras frases de elevada frequência.

## <a name="example"></a>Exemplo

JSON de exemplo

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
