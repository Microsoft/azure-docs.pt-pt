---
title: Alinhamento de palavras-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Para receber informações de alinhamento, use o método translate e inclua o parâmetro opcional includeAlignment.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837232"
---
# <a name="how-to-receive-word-alignment-information"></a>Como receber informações de alinhamento do Word

## <a name="receiving-word-alignment-information"></a>Recebendo informações de alinhamento do Word
Para receber informações de alinhamento, use o método translate e inclua o parâmetro opcional includeAlignment.

## <a name="alignment-information-format"></a>Formato de informações de alinhamento
O alinhamento é retornado como um valor de cadeia de caracteres do formato a seguir para cada palavra da origem. As informações de cada palavra são separadas por um espaço, incluindo linguagens não separadas por espaço (scripts), como chinês:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Cadeia de caracteres de alinhamento de exemplo: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Em outras palavras, os dois-pontos separa o índice inicial e final, o traço separa os idiomas e o espaço separa as palavras. Uma palavra pode ser alinhada com zero, uma ou várias palavras na outra linguagem, e as palavras alinhadas podem ser não contíguas. Quando nenhuma informação de alinhamento estiver disponível, o elemento Alignment estará vazio. O método não retorna nenhum erro nesse caso.

## <a name="restrictions"></a>Restrições
O alinhamento só é retornado para um subconjunto dos pares de idiomas neste ponto:
* do inglês para qualquer outro idioma;
* de qualquer outro idioma para inglês, exceto para chinês simplificado, chinês tradicional e Letão para inglês
* de japonês a coreano ou de coreano para japonês, você não receberá informações de alinhamento se a frase for uma tradução gravada. Um exemplo de uma tradução gravada é "Este é um teste", "Eu adoro você" e outras frases de alta frequência.

## <a name="example"></a>Exemplo

Exemplo de JSON

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
