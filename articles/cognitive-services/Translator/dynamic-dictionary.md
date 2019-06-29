---
title: Dicionário dinâmico - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Como utilizar a funcionalidade de dicionário dinâmica da API de texto do Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2ef1e474dd5d36f1967501ea7bdedc4736954a2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436017"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como utilizar um dicionário dinâmico

Se já sabe a tradução que pretende aplicar a uma palavra ou frase, pode fornecer como marcação dentro do pedido. O dicionário dinâmico só é seguro para substantivos compostos, como nomes próprios e nomes de produtos.

**Sintaxe:**

< a tradução de mstrans:dictionary = "tradução de frase" > frase < / mstrans:dictionary >

**Exemplo: en-de:**

Entrada de origem: A palavra < mstrans:dictionary tradução =\"wordomatic\"> palavra ou frase < / mstrans:dictionary > é uma entrada de dicionário.

Saída de destino: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Esta funcionalidade funciona da mesma forma com e sem modo HTML.

O recurso deve ser usado com moderação. A forma adequada e muito melhor de personalização de tradução é com o Translator personalizado. Tradutor personalizado utiliza completa de contexto e probabilidades de estatísticas. Se tiver ou pode criar dados de treinamento que mostra a sua empresa ou frase no contexto, obtém resultados muito melhores. Pode encontrar mais informações sobre o Translator personalizado na [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
