---
title: Conversão de dados - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como as expressões podem ser alteradas antes das previsões em Compreensão linguística (LUIS)
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b305be693f59b65a62570f656a0132f4f03cf099
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541803"
---
# <a name="convert-data-format-of-utterances"></a>Converter formato de dados de expressões
A LUIS fornece as seguintes conversões de uma expressão de utilizador antes da previsão"

* Discurso a texto utilizando o serviço [de discurso dos serviços cognitivos.](../Speech-Service/overview.md)

## <a name="speech-to-text"></a>Conversão de voz em texto

A fala ao texto é fornecida como uma integração com o LUIS.

### <a name="intent-conversion-concepts"></a>Conceitos de conversão de intenção
A conversão da fala para texto em LUIS permite-lhe enviar declarações faladas para um ponto final e receber uma resposta de previsão LUIS. O processo é uma integração do serviço [de Discurso](https://docs.microsoft.com/azure/cognitive-services/Speech) com o LUIS. Saiba mais sobre Discurso a Intenção com um [tutorial.](../speech-service/how-to-recognize-intents-from-speech-csharp.md)

### <a name="key-requirements"></a>Requisitos chave
Não precisa de criar uma chave **de API de discurso bing** para esta integração. Uma chave **de compreensão de linguagem** criada no portal Azure funciona para esta integração. Não utilize a chave de arranque LUIS.

### <a name="pricing-tier"></a>Escalão de Preço
Esta integração utiliza um modelo [de preços](luis-limits.md#key-limits) diferente dos níveis habituais de compreensão da linguagem.

### <a name="quota-usage"></a>Utilização de quotas
Consulte [os limites da chave](luis-limits.md#key-limits) para obter informações.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Extração de dados](luis-concept-data-extraction.md)

