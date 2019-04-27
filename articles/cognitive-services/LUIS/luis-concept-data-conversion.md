---
title: Conversão de dados
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba como expressões podem ser alterados antes de previsões de indisponibilidade na compreensão de idiomas (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: bdf2d87e558726937fa4221a0d95179216c66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813808"
---
# <a name="convert-data-format-of-utterances"></a>Converter o formato de dados de expressões
LUIS utiliza o serviço de voz de serviços cognitivos para converter expressões com expressões faladas em expressões de texto antes de predição. 

## <a name="speech-to-intent-conversion-concepts"></a>Conversão de voz em conceitos de conversão de intenção
Conversão de voz para texto em LUIS permite-lhe enviar expressões faladas com um ponto de extremidade e receber uma resposta de predição do LUIS. O processo é uma integração entre o [voz](https://docs.microsoft.com/azure/cognitive-services/Speech) serviço com os LUIS. Saiba mais sobre a conversão de voz em intenção com um [tutorial](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Requisitos chave
Não é necessário criar uma **API de voz do Bing** chave para essa integração. R **compreensão de idiomas** chave criada no portal do Azure funciona para essa integração. Não utilize a chave de arranque do LUIS.

### <a name="pricing-tier"></a>Escalão de Preço
Esta integração utiliza outra [preços](luis-boundaries.md#key-limits) modelos que o habitual compreensão de idiomas escalões de preço. 

### <a name="quota-usage"></a>Utilização da quota
Ver [limites de chaves](luis-boundaries.md#key-limits) para obter informações. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilize a voz para texto](luis-tutorial-speech-to-intent.md)

