---
title: Dicionário dinâmico - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Como utilizar a funcionalidade de dicionário dinâmica da API de texto do Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 0b4362d78ef105c249aafb4c6b203f69754a56c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507985"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Como utilizar a funcionalidade de dicionário dinâmica da API de texto do tradutor

Se já sabe a tradução que pretende aplicar a uma palavra ou frase, pode fornecer como marcação dentro do pedido. O dicionário dinâmico só é seguro para substantivos compostos, como nomes próprios e nomes de produtos.

**Sintaxe:**

< a tradução de mstrans:dictionary = "tradução de frase" > frase < / mstrans:dictionary >

**Exemplo: en-de:**

Entrada de origem: A palavra < mstrans:dictionary tradução =\"wordomatic\"> palavra ou frase < / mstrans:dictionary > é uma entrada de dicionário.

Saída de destino: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Esta funcionalidade funciona da mesma forma com e sem modo HTML.

O recurso deve ser usado com moderação. A forma adequada e muito melhor de personalização de tradução é com o Translator personalizado. Tradutor personalizado utiliza completa de contexto e probabilidades de estatísticas. Se tiver ou pode criar dados de treinamento que mostra a sua empresa ou frase no contexto, obtém resultados muito melhores. Pode encontrar mais informações sobre o Translator personalizado na [ https://aka.ms/CustomTranslator ](https://aka.ms/CustomTranslator).
