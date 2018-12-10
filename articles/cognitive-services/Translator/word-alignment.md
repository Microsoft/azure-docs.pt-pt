---
title: Alinhamento de palavra - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Receba informações de alinhamento do word da API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: v-jansko
ms.custom: seodec18
ms.openlocfilehash: 74e26d4bb9b9c901524a85248dbf7f9d6ead5e95
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091480"
---
# <a name="how-to-receive-word-alignment-information"></a>Como receber informações de alinhamento do word

## <a name="receiving-word-alignment-information"></a>Receba informações de alinhamento do word
Para receber informações de alinhamento, utilize o método Translate e inclua o parâmetro opcional includeAlignment.

## <a name="alignment-information-format"></a>Formato de informações de alinhamento
Alinhamento é retornado como um valor de cadeia de caracteres no seguinte formato para todas as palavras da origem. As informações para cada palavra são separadas por um espaço, incluindo o não-separadas por espaços linguagens (scripts), como o chinês:

[[SourceTextStartIndex]: [SourceTextEndIndex]-[TgtTextStartIndex]: [TgtTextEndIndex]] *

Cadeia de caracteres de alinhamento de exemplo: "0:0-7:10:2 de 1 a 11:20 3:4-0:3 a 4 de 3:4:5: 5-6 21:21".

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
