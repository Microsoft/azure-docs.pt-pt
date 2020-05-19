---
title: Dicionário Dinâmico - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar a característica dinâmica do dicionário do Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3ef881efc417a6c3816a92d9af7d77bba74355aa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592547"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se já sabe a tradução que pretende aplicar a uma palavra ou a uma frase, pode fornecê-la como marcação dentro do pedido. O dicionário dinâmico é seguro apenas para substantivos compostos como nomes apropriados e nomes de produtos.

**Sintaxe:**

<tradução do dicionário="tradução da frase">frase</mstrans:dicionário>

**Requisitos:**

* As `From` `To` línguas e as línguas devem incluir o inglês e outra língua apoiada. 
* Deve incluir o `From` parâmetro no seu pedido de tradução API em vez de utilizar a função de deteção automática. 

**Exemplo: en-de:**

Entrada de origem:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Saída do alvo:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Esta funcionalidade funciona da mesma forma com e sem modo HTML.

Utilize a função com moderação. Uma melhor maneira de personalizar a tradução é usando tradutor personalizado. Tradutor personalizado faz uso total do contexto e das probabilidades estatísticas. Se tiver ou puder criar dados de formação que mostram o seu trabalho ou frase em contexto, obtém resultados muito melhores. Pode encontrar mais informações sobre tradutor personalizado em [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .