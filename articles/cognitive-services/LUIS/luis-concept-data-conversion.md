---
title: Conversão de dados - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como as expressões podem ser alteradas antes das previsões na Compreensão da Linguagem (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: aeb9094db83b14af988f70485788934a7854200c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68619799"
---
# <a name="convert-data-format-of-utterances"></a>Converter formato de dados de expressões
LUIS fornece as seguintes conversões de uma expressão de utilizador antes da previsão"

* Discurso ao texto usando o serviço de [discurso dos serviços cognitivos.](../Speech-Service/overview.md) 

## <a name="speech-to-text"></a>Conversão de voz em texto

O discurso ao texto é fornecido como uma integração com o LUIS. 

### <a name="intent-conversion-concepts"></a>Conceitos de conversão de intenções
A conversão da fala para texto em LUIS permite-lhe enviar palavras faladas para um ponto final e receber uma resposta de previsão do LUIS. O processo é uma integração do serviço [de Fala](https://docs.microsoft.com/azure/cognitive-services/Speech) com a LUIS. Saiba mais sobre Discurso à Intenção com um [tutorial.](../speech-service/how-to-recognize-intents-from-speech-csharp.md)

### <a name="key-requirements"></a>Requisitos chave
Não é necessário criar uma chave **DaPi** bing speech para esta integração. Uma chave **de compreensão da linguagem** criada no portal Azure funciona para esta integração. Não utilize a tecla de arranque LUIS.

### <a name="pricing-tier"></a>Escalão de Preço
Esta integração utiliza um modelo de [preços](luis-boundaries.md#key-limits) diferente dos níveis habituais de preços da Compreensão da Linguagem. 

### <a name="quota-usage"></a>Utilização de quotas
Consulte [os limites de](luis-boundaries.md#key-limits) informação. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extração de dados](luis-concept-data-extraction.md)

