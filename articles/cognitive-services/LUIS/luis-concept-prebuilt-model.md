---
title: Modelos pré-construídos - LUIS
titleSuffix: Azure Cognitive Services
description: Os modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um domínio pré-construído ou adicionar um domínio relevante à sua aplicação mais tarde.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 6642e59c2957b298d54bc587853752b9fce74686
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019148"
---
# <a name="prebuilt-models"></a>Modelos pré-construídos

Os modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um modelo pré-construído ou adicionar um modelo relevante à sua aplicação mais tarde. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos pré-construídos

A LUIS fornece três tipos de modelos pré-construídos. Cada modelo pode ser adicionado à sua aplicação a qualquer momento. 

|Tipo de modelo|Inclusões|
|--|--|
|[Domínio](luis-reference-prebuilt-domains.md)|Intenções, declarações, entidades|
|Intenções|Intenções, declarações|
|[Entidades](luis-reference-prebuilt-entities.md)|Apenas entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-criados

A Compreensão de Línguas (LUIS) fornece *domínios pré-construídos,* que são modelos pré-treinados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que trabalham em conjunto para domínios ou categorias comuns de aplicações de clientes. 

Os domínios pré-construídos são treinados e prontos para adicionar à sua aplicação LUIS. As intenções e entidades de um domínio pré-construído são totalmente personalizáveis depois de as adicionar à sua app. 

> [!TIP]
> As intenções e entidades num domínio pré-construído funcionam melhor em conjunto. É melhor combinar intenções e entidades do mesmo domínio quando possível.
> O domínio pré-construído utilities tem intenções que pode personalizar para uso em qualquer domínio. Por exemplo, pode adicionar `Utilities.Repeat` à sua app e treiná-la reconhecendo quaisquer ações que o utilizador queira repetir na sua aplicação. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterar o comportamento de uma intenção de domínio pré-construído

Pode descobrir que um domínio pré-construído contém uma intenção semelhante a uma intenção que pretende ter na sua app LUIS, mas quer que se comporte de forma diferente. Por exemplo, o domínio pré-construído **Places** oferece a intenção de `MakeReservation` fazer uma reserva de restaurante, mas você quer que a sua app use essa intenção de fazer reservas de hotéis. Nesse caso, pode modificar o comportamento dessa intenção adicionando palavras de exemplo à intenção de fazer reservas de hotéis e, em seguida, retreinar a app. 

Pode encontrar uma lista completa dos domínios pré-construídos na [referência de domínios pré-construídos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intenções pré-construídas

A LUIS fornece intenções pré-construídas e as suas expressões para cada um dos seus domínios pré-construídos. As intenções podem ser adicionadas sem adicionar todo o domínio. Adicionar uma intenção é o processo de adicionar uma intenção e suas declarações à sua app. Tanto o nome da intenção como a lista de palavras podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-criadas

O LUIS inclui um conjunto de entidades pré-construídas para o reconhecimento de tipos comuns de informação, como datas, horários, números, medições e moeda. O suporte de entidade pré-construída varia conso pelo cultura da sua app LUIS. Para uma lista completa das entidades pré-construídas que a LUIS apoia, incluindo o apoio por cultura, consulte a [referência da entidade pré-construída.](./luis-reference-prebuilt-entities.md)

Quando uma entidade pré-construída está incluída na sua aplicação, as suas previsões estão incluídas na sua aplicação publicada. O comportamento das entidades pré-construídas é pré-treinado e **não pode** ser modificado. 

> [!NOTE]
> **builtin.datetime** é depreciado. É substituído por [**builtin.datetimeV2,**](luis-reference-prebuilt-datetimev2.md)que proporciona o reconhecimento das faixas de data e hora, bem como o reconhecimento melhorado de datas e horários ambíguos.

## <a name="next-steps"></a>Próximos passos

Saiba como [adicionar entidades pré-construídas](./howto-add-prebuilt-models.md) à sua aplicação.