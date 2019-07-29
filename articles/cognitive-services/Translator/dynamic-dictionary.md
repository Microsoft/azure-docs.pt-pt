---
title: Dicionário dinâmico-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Como usar o recurso de dicionário dinâmico do API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f586702350b38c6d546767f64a8e2f6115b59817
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595299"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para substantivos compostos, como nomes e nomes de produtos adequados.

**Sintaxe**

< msTrans: Dictionary translation = "conversão de frase" > frase </msTrans: Dicionário >

**Requirement**

* Os `From` idiomas `To` e devem ser diferentes. 
* Você deve incluir o `From` parâmetro em sua solicitação de tradução de API em vez de usar o recurso de detecção automática. 

**Exemplo: en-de:**

Entrada de origem: A palavra < msTrans: Dictionary translation =\"WordOMatic\"> Word ou frase </msTrans: Dictionary > é uma entrada de dicionário.

Saída de destino: Das Wort "WordOMatic" ist ein Wörterbucheintrag.

Esse recurso funciona da mesma maneira com e sem o modo HTML.

O recurso deve ser usado com moderação. A maneira apropriada e bem melhor de personalizar a tradução é usando o tradutor personalizado. O tradutor personalizado faz uso total de probabilidades de contexto e estatística. Se você tiver ou puder criar dados de treinamento que mostrem seu trabalho ou frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o tradutor personalizado [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)em.
