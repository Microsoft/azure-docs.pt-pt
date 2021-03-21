---
title: Dicionário Dinâmico - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar a característica dinâmica do dicionário do Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: ebfccb6fed46db65f6a70937516552f5280c8016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897769"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se já sabe a tradução que pretende aplicar a uma palavra ou frase, pode fornecê-la como marcação dentro do pedido. O dicionário dinâmico é seguro apenas para substantivos compostos como nomes próprios e nomes de produtos.

**Sintaxe:**

<mstrans:tradução do dicionário="tradução da frase">frase</mstrans:dicionário>

**Requisitos:**

* As `From` `To` línguas e as línguas devem incluir o inglês e outra língua apoiada. 
* Deve incluir o `From` parâmetro no seu pedido de tradução API em vez de utilizar a função de autodetect. 

**Exemplo: en-de:**

Entrada de origem: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Saída do alvo: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Esta função funciona da mesma forma com e sem o modo HTML.

Utilize a funcionalidade com moderação. Uma forma melhor de personalizar a tradução é utilizando o Tradutor Personalizado. O Tradutor Personalizado faz uso total do contexto e das probabilidades estatísticas. Se tiver ou puder criar dados de treino que mostrem o seu trabalho ou frase em contexto, obtém resultados muito melhores. Pode encontrar mais informações sobre o Tradutor Personalizado em [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .