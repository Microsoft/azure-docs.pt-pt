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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264385"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Modelos predefinidos de domínio, intenção e entidade

Os modelos predefinidos fornecem domínios, intenções, declarações e entidades. Você pode iniciar seu aplicativo com um domínio predefinido ou adicionar um domínio relevante ao seu aplicativo posteriormente. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos predefinidos

Há três tipos de modelos predefinidos que o LUIS fornece. Cada modelo pode ser adicionado ao seu aplicativo a qualquer momento. 

|Tipo de modelo|Inclui|
|--|--|
|Domain|Intenções, declarações, entidades|
|Intenções|Intenções, declarações|
|Entidades|Somente entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-criados

O Reconhecimento vocal (LUIS) fornece *domínios pré-criados*, que são conjuntos pré-criados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que funcionam em conjunto para domínios ou categorias comuns de aplicativos cliente. 

Os domínios predefinidos são treinados e estão prontos para serem adicionados ao seu aplicativo LUIS. As intenções e entidades em um domínio predefinido são totalmente personalizáveis depois de serem adicionadas ao seu aplicativo. 

Se você começar a personalizar um domínio predefinido inteiro, exclua as tentativas e entidades que seu aplicativo não precisa usar. Você também pode adicionar algumas intenções ou entidades ao conjunto que o domínio predefinido já fornece. Por exemplo, se você estiver usando o domínio predefinido **eventos** para um aplicativo de evento esportivo, poderá adicionar entidades para equipes esportivas. Quando você começa a [fornecer declarações](luis-how-to-add-example-utterances.md) para Luis, inclua termos específicos para seu aplicativo. LUIS aprende a reconhecê-los e caudas das intenções e entidades do domínio predefinido para as necessidades do seu aplicativo. 

> [!TIP]
> As intenções e entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar tentativas e entidades do mesmo domínio quando possível.
> O domínio predefinido utilitários tem intenções que você pode personalizar para uso em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo a reconhecer qualquer ação que o usuário queira repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinida

Você pode achar que um domínio predefinido contém uma intenção semelhante a uma intenção que você deseja ter em seu aplicativo LUIS, mas deseja que ele se comporte de forma diferente. Por exemplo, o domínio predefinido **locais** fornece uma intenção `MakeReservation` para fazer uma reserva de restaurante, mas você deseja que seu aplicativo use essa intenção para fazer reservas de Hotel. Nesse caso, você pode modificar o comportamento dessa intenção fornecendo declarações para LUIS sobre como fazer reservas de Hotel e rotulá-las usando a intenção `MakeReservation`, então, LUIS pode ser retreinado para reconhecer a intenção de `MakeReservation` em uma solicitação para agendar um hotel.

Você pode encontrar uma lista completa dos domínios predefinidos na [referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Tentativas predefinidas

O LUIS fornece tentativas predefinidas e suas declarações. As intenções podem ser adicionadas sem adicionar o domínio inteiro. Adicionar uma intenção é o processo de adicionar uma intenção e seu declarações. O nome da intenção e a lista expressão podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-criadas

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações, como datas, horas, números, medidas e moeda. O suporte à entidade predefinida varia de acordo com a cultura do seu aplicativo LUIS. Para obter uma lista completa das entidades predefinidas que o LUIS dá suporte, incluindo suporte por cultura, consulte a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas no aplicativo publicado. O comportamento de entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **Builtin. DateTime** foi preterido. Ele é substituído por [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece reconhecimento de intervalos de data e hora, bem como o reconhecimento aprimorado de datas e horários ambíguos.

## <a name="next-steps"></a>Passos seguintes

Saiba como [adicionar entidades predefinidas](luis-prebuilt-entities.md) ao seu aplicativo.
