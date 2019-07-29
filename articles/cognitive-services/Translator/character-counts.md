---
title: Contagens de caracteres-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Como o API de Tradução de Texto conta caracteres.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595983"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como o API de Tradução de Texto conta caracteres

A API de Tradução de Texto conta cada ponto de código Unicode de texto de entrada como um caractere. Cada tradução de um texto para um idioma conta como uma tradução separada, mesmo que a solicitação tenha sido feita em uma única chamada à API que seja traduzida para vários idiomas. O comprimento da resposta não importa.

Quais são as contagens:

* Texto passado para o API de Tradução de Texto no corpo da solicitação
   * `Text`ao usar os métodos translate, exliterate e Lookup de dicionário
   * `Text`e `Translation` ao usar o método de exemplos de dicionário
* Todas as marcações: HTML, marcas XML, etc. dentro do campo de texto do corpo da solicitação. A notação JSON usada para criar a solicitação (por exemplo, "Text:") não é contada.
* Uma letra individual
* Pontuação
* Um espaço, uma tabulação, uma marcação e qualquer tipo de caractere de espaço em branco
* Todos os pontos de código definidos em Unicode
* Uma tradução repetida, mesmo que você tenha traduzido o mesmo texto anteriormente

Para scripts baseados em ideograms como chinês e japonês kanji, o API de Tradução de Texto ainda contará o número de pontos de código Unicode, um caractere por ideogram. Exceção: Os substitutos Unicode contam como dois caracteres.

O número de solicitações, palavras, bytes ou sentenças é irrelevante na contagem de caracteres.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caracteres. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence estejam em uma proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence que você fizer exceder o número de outros métodos contados por 100 vezes, a Microsoft se reservará o direito de restringir o uso dos métodos Detect e BreakSentence.


Mais informações sobre contagens de caracteres estão nas [perguntas frequentes sobre o Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
