---
title: Alinhamento de palavras - Tradutor
titleSuffix: Azure Cognitive Services
description: Para receber informações de alinhamento, utilize o método Traduzir e inclua o parâmetro opcional de alinhamento.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: aa43312e16b2c1777ab9a1f1668cc4ea9c9d18e4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588467"
---
# <a name="how-to-receive-word-alignment-information"></a>Como receber informações de alinhamento de palavras

## <a name="receiving-word-alignment-information"></a>Receber informações de alinhamento de palavras
Para receber informações de alinhamento, utilize o método Traduzir e inclua o parâmetro opcional de alinhamento.

## <a name="alignment-information-format"></a>Formato de informação de alinhamento
O alinhamento é devolvido como um valor de cadeia do seguinte formato para cada palavra da fonte. A informação para cada palavra é separada por um espaço, incluindo para línguas não separadas do espaço (scripts) como o chinês:

[[SourceTextStartIndex]:[SourceTextEndIndex]-[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exemplo de alinhamento: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Por outras palavras, o cólon separa o índice de início e fim, o traço separa as línguas, e o espaço separa as palavras. Uma palavra pode alinhar-se com zero, uma ou múltiplas palavras na outra língua, e as palavras alinhadas podem não ser contíguas. Quando não houver informação de alinhamento disponível, o elemento Alinhamento estará vazio. O método não devolve nenhum erro nesse caso.

## <a name="restrictions"></a>Restrições
O alinhamento só é devolvido para um subconjunto dos pares linguísticos neste momento:
* do inglês para qualquer outra língua;
* de qualquer outra língua para inglês, exceto para chinês simplificado, chinês tradicional, e letão para inglês
* De japonês a coreano ou de coreano para japonês Não receberá informações de alinhamento se a frase for uma tradução enlatada. Exemplo de uma tradução enlatada é "Isto é um teste", "Amo-te", e outras frases de alta frequência.

## <a name="example"></a>Exemplo

Exemplo JSON

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
