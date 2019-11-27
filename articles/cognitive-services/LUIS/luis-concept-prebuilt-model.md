---
title: Modelos predefinidos – LUIS
titleSuffix: Azure Cognitive Services
description: Modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um domínio pré-criado ou adicionar um domínio relevante para a sua aplicação mais tarde.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280843"
---
# <a name="prebuilt-models"></a>Modelos pré-construídos

Modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Você pode iniciar seu aplicativo com um modelo predefinido ou adicionar um modelo relevante ao seu aplicativo posteriormente. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos pré-criados

O LUIS fornece três tipos de modelos pré-criados. Cada modelo pode ser adicionado à sua aplicação em qualquer altura. 

|Tipo de modelo|Inclui|
|--|--|
|[Controlador](luis-reference-prebuilt-domains.md)|Objetivos, expressões, entidades|
|Intenções|Objetivos, expressões com|
|[Contabilidade](luis-reference-prebuilt-entities.md)|Apenas as entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-criados

O Reconhecimento vocal (LUIS) fornece *domínios*pré-criados, que são modelos pré-instalados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que funcionam em conjunto para domínios ou categorias comuns de aplicativos cliente. 

Os domínios criados previamente são treinados e pronto para adicionar à sua aplicação LUIS. As intenções e entidades de um domínio predefinido são totalmente personalizáveis depois de serem adicionadas ao seu aplicativo. 

> [!TIP]
> Os objetivos e entidades num domínio pré-criado funcionam melhor em conjunto. É melhor combinar as intenções e entidades do mesmo domínio sempre que possível.
> O domínio pré-criado utilitários tem intenções que pode personalizar para utilização em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo a reconhecer qualquer ação que o usuário queira repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterar o comportamento de uma intenção de domínio pré-criado

Pode achar que um domínio pré-criado contém um objetivo semelhante a um objetivo a que pretende ter na sua aplicação LUIS, mas deseja ter um comportamento diferente. Por exemplo, o domínio predefinido **coloca** uma `MakeReservation` intenção de fazer uma reserva de restaurante, mas você deseja que seu aplicativo use essa intenção para fazer reservas de Hotel. Nesse caso, você pode modificar o comportamento dessa intenção adicionando o exemplo declarações à intenção de fazer reservas de Hotel e, em seguida, treinar novamente o aplicativo. 

Você pode encontrar uma lista completa dos domínios predefinidos na [referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Objetivos pré-criados

O LUIS fornece tentativas predefinidas e suas declarações para cada um de seus domínios predefinidos. Objetivos podem ser adicionados sem adicionar o domínio inteiro. Adicionar uma intenção é o processo de adicionar uma intenção e seu declarações ao seu aplicativo. O nome de intenção e a lista de expressão podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-criadas

LUIS inclui um conjunto de entidades previamente concebidas para reconhecimento de tipos comuns de informações, como datas, horas, números, medidas e moeda. Suporte de entidade pré-criados varia consoante a cultura da sua aplicação LUIS. Para obter uma lista completa das entidades predefinidas que o LUIS dá suporte, incluindo suporte por cultura, consulte a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade pré-criados é incluída na sua aplicação, respetivas previsões são incluídos em seu aplicativo publicado. O comportamento de entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **Builtin. DateTime** foi preterido. Ele é substituído por [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece reconhecimento de intervalos de data e hora, bem como o reconhecimento aprimorado de datas e horários ambíguos.

## <a name="next-steps"></a>Passos seguintes

Saiba como [adicionar entidades predefinidas](luis-prebuilt-entities.md) ao seu aplicativo.
