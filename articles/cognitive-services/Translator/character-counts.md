---
title: Contagem de Caracteres - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como o Tradutor de Serviços Cognitivos Azure conta caracteres para que possa entender como ingere conteúdo.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373932"
---
# <a name="how-the-translator-counts-characters"></a>Como o Tradutor conta personagens

O Tradutor conta cada ponto de código unicode do texto de entrada como um personagem. Cada tradução de um texto para uma língua conta como uma tradução separada, mesmo que o pedido tenha sido feito numa única chamada de API traduzindo para várias línguas. A duração da resposta não importa.

O que conta é:

* Texto passado ao Tradutor no corpo do pedido
   * `Text` ao utilizar os métodos de Tradução, Transliteração e Programação do Dicionário
   * `Text` e `Translation` ao usar o método Exemplos do Dicionário
* Todas as marcações: HTML, etiquetas XML, etc. dentro do campo de texto do corpo de pedido. A notação JSON utilizada para construir o pedido (por exemplo, "Texto:") não é contada.
* Uma carta individual
* Pontuação
* Um espaço, separador, marcação, e qualquer tipo de personagem de espaço branco
* Todos os pontos de código definidos no Unicode
* Uma tradução repetida, mesmo que tenha traduzido o mesmo texto anteriormente

Para scripts baseados em ideogramas como o chinês e o japonês Kanji, o serviço Tradutor ainda conta o número de pontos de código Unicode, um personagem por ideograma. Exceção: Os substitutos do Unicode contam como dois caracteres.

O número de pedidos, palavras, bytes ou frases é irrelevante na contagem de caracteres.

As chamadas para os métodos Deteção e Quebra de Probabilidades não são contabilizadas no consumo de caracteres. No entanto, esperamos que as chamadas para os métodos De Deteção e Quebra de Probabilidades sejam de uma proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence que eserir exceder o número de outros métodos contados em 100 vezes, a Microsoft reserva-se o direito de restringir a sua utilização dos métodos De deteção e Quebra.

Cada personagem submetido à função de tradução é contado mesmo quando o conteúdo não é alterado ou quando a origem e o idioma alvo são os mesmos.

Mais informações sobre a contagem de caracteres está na [FAQ tradutor.](https://www.microsoft.com/en-us/translator/faq.aspx)
