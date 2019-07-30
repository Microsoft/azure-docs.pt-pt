---
title: Conversão de dados-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como expressões podem ser alterados antes de previsões de indisponibilidade na compreensão de idiomas (LUIS)
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
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619799"
---
# <a name="convert-data-format-of-utterances"></a>Converter o formato de dados de expressões
O LUIS fornece as seguintes conversões de um usuário expressão antes da previsão "

* Fala para texto usando o serviço de [fala de serviços cognitivas](../Speech-Service/overview.md) . 

## <a name="speech-to-text"></a>Conversão de voz em texto

A fala em texto é fornecida como uma integração com o LUIS. 

### <a name="intent-conversion-concepts"></a>Conceitos de conversão de intenção
Conversão de voz para texto em LUIS permite-lhe enviar expressões faladas com um ponto de extremidade e receber uma resposta de predição do LUIS. O processo é uma integração entre o [voz](https://docs.microsoft.com/azure/cognitive-services/Speech) serviço com os LUIS. Saiba mais sobre a fala para fazer uma tentativa com um [tutorial](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Requisitos chave
Não é necessário criar uma **API de voz do Bing** chave para essa integração. R **compreensão de idiomas** chave criada no portal do Azure funciona para essa integração. Não use a chave de início LUIS.

### <a name="pricing-tier"></a>Escalão de Preço
Essa integração usa um modelo de [preços](luis-boundaries.md#key-limits) diferente dos tipos de preço de reconhecimento vocal usuais. 

### <a name="quota-usage"></a>Utilização da quota
Ver [limites de chaves](luis-boundaries.md#key-limits) para obter informações. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extraindo dados](luis-concept-data-extraction.md)

