---
title: Contagem de Caracteres - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como o Tradutor de Serviços Cognitivos Azure conta personagens para que possa entender como ingere o conteúdo.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 0219940a4ac60e4a6187d13802c36e8bff1925b3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587328"
---
# <a name="how-the-translator-counts-characters"></a>Como o Tradutor conta personagens

O Tradutor conta todos os pontos de código unicódigo do texto de entrada como um personagem. Cada tradução de um texto para uma língua conta como uma tradução separada, mesmo que o pedido tenha sido feito numa única chamada da API traduzindo para várias línguas. A duração da resposta não importa.

O que conta é:

* Texto passado a Tradutor no corpo do pedido
   * `Text`ao utilizar os métodos de procura de tradução, transliterato e dicionário
   * `Text`e `Translation` ao utilizar o método exemplos do dicionário
* Todas as marcações: HTML, etiquetas XML, etc. no campo de texto do organismo de pedido. Notação JSON utilizada para construir o pedido (por exemplo"Texto:") não é contada.
* Uma letra individual
* Pontuação
* Um espaço, separador, marcação, e qualquer tipo de personagem de espaço branco
* Cada ponto de código definido no Unicode
* Uma tradução repetida, mesmo que tenha traduzido o mesmo texto anteriormente

Para scripts baseados em ideogramas como o chinês e o japonês Kanji, o serviço Tradutor ainda conta o número de pontos de código Unicode, um personagem por ideograma. Exceção: Os substitutos do Unicode contam como dois caracteres.

O número de pedidos, palavras, bytes ou frases é irrelevante na contagem de caracteres.

As chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caracteres. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam numa proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence que fizer exceder o número de outros métodos contados em 100 vezes, a Microsoft reserva-se o direito de restringir a sua utilização dos métodos Detect e BreakSentence.

Mais informações sobre contagens de caracteres está no [Tradutor FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
