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
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: be0ac73b1e225612fedb93c59a90380d67c6de92
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560710"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Modelos pré-construídos de domínio, a intenção e a entidade

Modelos pré-construídos fornecem domínios, intenções, expressões e entidades. Pode iniciar a sua aplicação com um domínio pré-criado ou adicionar um domínio relevante para a sua aplicação mais tarde. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos pré-criados

Existem 3 tipos de modelos pré-construídos que Luis fornece. Cada modelo pode ser adicionado à sua aplicação em qualquer altura. 

|Tipo de modelo|Inclui|
|--|--|
|Domain|Objetivos, expressões, entidades|
|Intenções|Objetivos, expressões com|
|Entidades|Apenas as entidades| 

## <a name="prebuilt-domains"></a>Domínios pré-concebidos

Compreensão de idiomas (LUIS) fornece *domínios pré-concebidos*, que são conjuntos pré-criados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que funcionam em conjunto para domínios ou categorias comuns de aplicações de cliente. 

Os domínios criados previamente são treinados e pronto para adicionar à sua aplicação LUIS. Os objetivos e entidades num domínio pré-criado são totalmente personalizáveis depois de adicioná-los à sua aplicação. 

Se iniciar a partir de personalizar um domínio pré-criado completo, elimine as intenções e entidades que seu aplicativo não precisa usar. Também pode adicionar alguns objetivos ou entidades para o conjunto de que o domínio pré-criado já fornece. Por exemplo, se estiver a utilizar o **eventos** domínio pré-criado para uma aplicação de eventos desportivos, pode adicionar entidades para as equipes do desporto. Quando inicia [fornecendo expressões com](luis-how-to-add-example-utterances.md) para LUIS, incluem os termos que são específicos para a sua aplicação. LUIS aprende a reconhecê-los e tailors do domínio pré-criado intenções e entidades às necessidades da sua aplicação. 

> [!TIP]
> Os objetivos e entidades num domínio pré-criado funcionam melhor em conjunto. É melhor combinar as intenções e entidades do mesmo domínio sempre que possível.
> O domínio pré-criado utilitários tem intenções que pode personalizar para utilização em qualquer domínio. Por exemplo, pode adicionar `Utilities.Repeat` à sua aplicação e train ele reconhece qualquer utilizador de ações, poderá querer repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterar o comportamento de uma intenção de domínio pré-criado

Pode achar que um domínio pré-criado contém um objetivo semelhante a um objetivo a que pretende ter na sua aplicação LUIS, mas deseja ter um comportamento diferente. Por exemplo, o **lugares** domínio pré-criado fornece um `MakeReservation` intenção para fazer uma reserva do restaurante, mas quiser a sua aplicação para utilizar esse propósito para fazer reservas de hotel. Nesse caso, pode modificar o comportamento desse objetivo, fornecendo expressões para LUIS sobre fazer reservas de hotel e a etiquetagem-los utilizando o `MakeReservation` intenção, então, LUIS pode reestruturar os reconheça o `MakeReservation` intenção num pedido para reservar um hotel .

Pode encontrar uma lista completa dos domínios criados previamente no [referência de domínios pré-concebidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Objetivos pré-criados

LUIS fornece intenções pré-criados e seus discursos. Objetivos podem ser adicionados sem adicionar o domínio inteiro. A adição de um objetivo é o processo de adicionar uma intenção e seus discursos. O nome de intenção e a lista de expressão podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades pré-concebidas

LUIS inclui um conjunto de entidades previamente concebidas para reconhecimento de tipos comuns de informações, como datas, horas, números, medidas e moeda. Suporte de entidade pré-criados varia consoante a cultura da sua aplicação LUIS. Para obter uma lista completa das entidades pré-criados que suporte o LUIS, incluindo o suporte por cultura, consulte a [referência de entidade pré-criados](./luis-reference-prebuilt-entities.md).

Quando uma entidade pré-criados é incluída na sua aplicação, respetivas previsões são incluídos em seu aplicativo publicado. O comportamento das entidades pré-concebidas é com formação prévia e **não é possível** ser modificado. Siga estes passos para ver como funciona uma entidade predefinida:

> [!NOTE]
> **Builtin.DateTime** foi preterido. Ele é substituído por [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece o reconhecimento de data e intervalos de tempo, bem como reconhecimento aperfeiçoado de ambíguas datas e horas.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [adicionar entidades pré-concebidas](luis-prebuilt-entities.md) à sua aplicação.
