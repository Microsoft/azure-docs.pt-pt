---
title: Contagem de caracteres - Tradutor Texto API
titleSuffix: Azure Cognitive Services
description: Este artigo explica como o Tradutor de Serviços Cognitivos Azure API conta caracteres para que possa entender como ingere o conteúdo.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888148"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como a API de Texto tradutor conta caracteres

O Tradutor Text API conta todos os pontos de código Unicode do texto de entrada como um personagem. Cada tradução de um texto para uma língua conta como uma tradução separada, mesmo que o pedido tenha sido feito numa única chamada da API traduzindo para várias línguas. A duração da resposta não importa.

O que conta é:

* Texto passado para a API de Texto tradutor no corpo do pedido
   * `Text`ao utilizar os métodos de procura de tradução, transliterato e dicionário
   * `Text`e `Translation` ao utilizar o método exemplos do dicionário
* Todas as marcações: HTML, etiquetas XML, etc. no campo de texto do organismo de pedido. Notação JSON utilizada para construir o pedido (por exemplo"Texto:") não é contada.
* Uma letra individual
* Pontuação
* Um espaço, separador, marcação, e qualquer tipo de personagem de espaço branco
* Cada ponto de código definido no Unicode
* Uma tradução repetida, mesmo que tenha traduzido o mesmo texto anteriormente

Para scripts baseados em ideogramas como o chinês e o japonês Kanji, o Tradutor Text API ainda contará o número de pontos de código Unicode, um personagem por ideograma. Exceção: Os substitutos do Unicode contam como dois caracteres.

O número de pedidos, palavras, bytes ou frases é irrelevante na contagem de caracteres.

As chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caracteres. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam numa proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence que fizer exceder o número de outros métodos contados em 100 vezes, a Microsoft reserva-se o direito de restringir a sua utilização dos métodos Detect e BreakSentence.


Mais informações sobre contagens de caracteres está no [Microsoft Tradutor FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
