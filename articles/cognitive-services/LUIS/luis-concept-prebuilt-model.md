---
title: Modelos predefinidos – LUIS
titleSuffix: Azure Cognitive Services
description: Os modelos predefinidos fornecem domínios, intenções, declarações e entidades. Você pode iniciar seu aplicativo com um domínio predefinido ou adicionar um domínio relevante ao seu aplicativo posteriormente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: d9cb86c1c19649052e4796fd0a8909ce08381d55
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487590"
---
# <a name="prebuilt-models"></a>Modelos pré-construídos

Os modelos predefinidos fornecem domínios, intenções, declarações e entidades. Você pode iniciar seu aplicativo com um modelo predefinido ou adicionar um modelo relevante ao seu aplicativo posteriormente. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos predefinidos

O LUIS fornece três tipos de modelos pré-criados. Cada modelo pode ser adicionado ao seu aplicativo a qualquer momento. 

|Tipo de modelo|Inclui|
|--|--|
|[Controlador](luis-reference-prebuilt-domains.md)|Intenções, declarações, entidades|
|Intenções|Intenções, declarações|
|[Contabilidade](luis-reference-prebuilt-entities.md)|Somente entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-criados

O Reconhecimento vocal (LUIS) fornece *domínios predefinidos*, que são modelos pretreinados de [tentativas](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que funcionam em conjunto para domínios ou categorias comuns de aplicativos cliente. 

Os domínios predefinidos são treinados e estão prontos para serem adicionados ao seu aplicativo LUIS. As intenções e entidades de um domínio predefinido são totalmente personalizáveis depois de serem adicionadas ao seu aplicativo. 

> [!TIP]
> As intenções e entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar tentativas e entidades do mesmo domínio quando possível.
> O domínio predefinido utilitários tem intenções que você pode personalizar para uso em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo a reconhecer qualquer ação que o usuário queira repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinida

Você pode achar que um domínio predefinido contém uma intenção semelhante a uma intenção que você deseja ter em seu aplicativo LUIS, mas deseja que ele se comporte de forma diferente. Por exemplo, o domínio predefinido **coloca** uma `MakeReservation` intenção de fazer uma reserva de restaurante, mas você deseja que seu aplicativo use essa intenção para fazer reservas de Hotel. Nesse caso, você pode modificar o comportamento dessa intenção adicionando o exemplo declarações à intenção de fazer reservas de Hotel. Em seguida, retreinado o aplicativo. 

Você pode encontrar uma lista completa dos domínios predefinidos na [referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Tentativas predefinidas

O LUIS fornece intenções predefinidas e seus declarações, dos domínios predefinidos. As intenções podem ser adicionadas sem adicionar o domínio inteiro. Adicionar uma intenção é o processo de adicionar uma intenção e seu declarações ao seu aplicativo. O nome da intenção e a lista expressão podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-criadas

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações, como datas, horas, números, medidas e moeda. O suporte à entidade predefinida varia de acordo com a cultura do seu aplicativo LUIS. Para obter uma lista completa das entidades predefinidas que o LUIS dá suporte, incluindo suporte por cultura, consulte a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas no aplicativo publicado. O comportamento de entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **Builtin. DateTime** foi preterido. Ele é substituído por [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece reconhecimento de intervalos de data e hora, bem como o reconhecimento aprimorado de datas e horários ambíguos.

## <a name="next-steps"></a>Passos seguintes

Saiba como [adicionar entidades predefinidas](luis-prebuilt-entities.md) ao seu aplicativo.
