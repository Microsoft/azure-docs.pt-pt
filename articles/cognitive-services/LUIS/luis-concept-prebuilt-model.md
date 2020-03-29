---
title: Modelos pré-construídos - LUIS
titleSuffix: Azure Cognitive Services
description: Os modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um domínio pré-construído ou adicionar um domínio relevante à sua aplicação mais tarde.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280843"
---
# <a name="prebuilt-models"></a>Modelos pré-construídos

Os modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um modelo pré-construído ou adicionar um modelo relevante à sua aplicação mais tarde. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos pré-construídos

A LUIS fornece três tipos de modelos pré-construídos. Cada modelo pode ser adicionado à sua aplicação a qualquer momento. 

|Tipo de modelo|Inclusões|
|--|--|
|[Domínio](luis-reference-prebuilt-domains.md)|Intenções, declarações, entidades|
|Intenções|Intenções, proclamações|
|[Entidades](luis-reference-prebuilt-entities.md)|Apenas entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-criados

A Compreensão da Linguagem (LUIS) fornece *domínios pré-construídos,* que são modelos pré-treinados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que trabalham em conjunto para domínios ou categorias comuns de aplicações de clientes. 

Os domínios pré-construídos são treinados e prontos para adicionar à sua aplicação LUIS. As intenções e entidades de um domínio pré-construído são totalmente personalizáveis depois de as adicionar à sua app. 

> [!TIP]
> As intenções e entidades num domínio pré-construído funcionam melhor em conjunto. É melhor combinar intenções e entidades do mesmo domínio quando possível.
> O domínio pré-construído utilities tem intenção que pode personalizar para uso em qualquer domínio. Por exemplo, pode `Utilities.Repeat` adicionar à sua aplicação e treiná-la reconhecendo quaisquer ações que o utilizador queira repetir na sua aplicação. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Mudar o comportamento de uma intenção de domínio pré-construído

Pode descobrir que um domínio pré-construído contém uma intenção semelhante a uma intenção que pretende ter na sua aplicação LUIS, mas quer que se comporcomode de forma diferente. Por exemplo, o domínio pré-construído **da Places** fornece uma `MakeReservation` intenção de fazer uma reserva de restaurante, mas você quer que a sua aplicação use essa intenção de fazer reservas de hotéis. Nesse caso, pode modificar o comportamento dessa intenção adicionando declarações de exemplo à intenção de fazer reservas de hotéis e, em seguida, retreinar a app. 

Pode encontrar uma listagem completa dos domínios pré-construídos na referência de [domínios pré-construídos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intenções pré-construídas

Luis fornece intenções pré-construídas e suas expressões para cada um dos seus domínios pré-construídos. As intenções podem ser adicionadas sem adicionar todo o domínio. Adicionar uma intenção é o processo de adicionar uma intenção e as suas declarações à sua app. Tanto o nome da intenção como a lista de pronunciamentopodem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-criadas

O LUIS inclui um conjunto de entidades pré-construídas para o reconhecimento de tipos comuns de informação, como datas, horários, números, medições e moeda. O suporte à entidade pré-construída varia pela cultura da sua app LUIS. Para uma lista completa das entidades pré-construídas que a LUIS apoia, incluindo o apoio à cultura, consulte a referência da [entidade pré-construída.](./luis-reference-prebuilt-entities.md)

Quando uma entidade pré-construída está incluída na sua aplicação, as suas previsões estão incluídas na sua aplicação publicada. O comportamento das entidades pré-construídas é pré-treinado e **não pode** ser modificado. 

> [!NOTE]
> **builtin.datetime** é depreciado. É substituído por [**builtin.datetimeV2,**](luis-reference-prebuilt-datetimev2.md)que proporciona o reconhecimento das gamas de data e hora, bem como um melhor reconhecimento de datas e horários ambíguos.

## <a name="next-steps"></a>Passos seguintes

Saiba como [adicionar entidades pré-construídas](luis-prebuilt-entities.md) à sua aplicação.
