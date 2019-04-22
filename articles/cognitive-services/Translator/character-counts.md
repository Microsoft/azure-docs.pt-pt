---
title: Caráter contagens - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Como a API de texto do Translator contabiliza os carateres.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: c88eb56288d3a7cf46ce84430a53c12a4ee31c7a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58917418"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como a API de texto do Translator contabiliza os carateres

A API de texto do Translator contabiliza cada ponto de código Unicode de texto de entrada como um caractere. Cada tradução de um texto para um idioma é contabilizado como uma tradução separada, mesmo que o pedido foi feito por uma única API chamada a tradução para vários idiomas. O comprimento da resposta não é relevante.

O que conta é:

* Texto transmitido para a API de texto do Translator no corpo do pedido
   * `Text` ao utilizar os métodos Translate Transliterate e pesquisa de dicionário
   * `Text` e `Translation` ao usar o método de exemplos de dicionário
* Todas as marcações: HTML, marcas XML, etc. no campo de texto do corpo do pedido. Notação de JSON utilizada para criar o pedido (por exemplo "texto:") não é contabilizado.
* Uma letra individual
* Pontuação
* Um espaço, separador, marcação e qualquer tipo de caractere de espaço em branco
* Cada ponto de código definido em Unicode
* Uma tradução repetida, até mesmo se tiver convertido o mesmo texto anteriormente

Para os scripts com base em ideogramas como chinês e japonês Kanji, a API de texto do Translator ainda irá contar o número de pontos de código Unicode, um caráter por ideogram. Exceção: Substitutos Unicode contagem como dois caracteres.

O número de pedidos, palavras, bytes ou frases é irrelevante na contagem de caracteres. 

Chamadas para os métodos de deteção e BreakSentence não são contabilizadas no consumo de caráter. No entanto, esperamos que as chamadas para os métodos de deteção e BreakSentence estão numa proporção razoável para a utilização de outras funções que são contadas. Se o número de chamadas de deteção ou BreakSentence que fizer excede o número de outros métodos contados por 100 vezes, Microsoft se reserva o direito para restringir a utilização dos métodos de deteção e BreakSentence.


Obter mais informações sobre as contagens de caracteres são no [FAQ do Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
