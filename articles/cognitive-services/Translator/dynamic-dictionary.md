---
title: Dicionário dinâmico-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar o recurso de dicionário dinâmico do API de Tradução de Texto de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837346"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para substantivos compostos, como nomes e nomes de produtos adequados.

**Sintaxe**

< msTrans: Dictionary translation = "conversão de frase" > frase </msTrans: Dicionário >

**Requirement**

* As linguagens `From` e `To` devem ser diferentes. 
* Você deve incluir o parâmetro `From` em sua solicitação de tradução de API em vez de usar o recurso de detecção automática. 

**Exemplo: en-de:**

Entrada de origem: a palavra < msTrans: dicionário translation =\"WordOMatic\"> palavra ou frase </msTrans: Dictionary > é uma entrada de dicionário.

Saída de destino: das Wort "WordOMatic" ist ein Wörterbucheintrag.

Esse recurso funciona da mesma maneira com e sem o modo HTML.

O recurso deve ser usado com moderação. A maneira apropriada e bem melhor de personalizar a tradução é usando o tradutor personalizado. O tradutor personalizado faz uso total de probabilidades de contexto e estatística. Se você tiver ou puder criar dados de treinamento que mostrem seu trabalho ou frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o tradutor personalizado em [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
