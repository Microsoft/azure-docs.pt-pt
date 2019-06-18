---
title: Funções para entidades
titleSuffix: Azure Cognitive Services
description: As funções são subtipos contextuais, com o nome de uma entidade usado apenas em padrões. Por exemplo, na expressão `buy a ticket from New York to London`, nova York e Londres cidades mas cada um tem um significado diferente da sentença. Nova Iorque é a cidade de origem e Londres é a cidade de destino.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 318e71b68bbabeeef34c75a412f9fdd5b6db754a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073016"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Funções de entidade para subtipos contextuais

Funções permitem que as entidades ter o nome subtipos. Uma função pode ser utilizada com qualquer tipo de entidade predefinidos ou personalizados e utilizada em expressões de exemplo e padrões. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exemplo de entidade aprendidas de máquina de funções

Na expressão "comprar uma permissão de **Nova Iorque** ao **Londres**, nova York e Londres cidades mas cada um tem um significado diferente da sentença. Nova Iorque é a cidade de origem e Londres é a cidade de destino. 

```
buy a ticket from New York to London
```

As funções oferecem um nome para essas diferenças:

|Tipo de entidade|Nome da entidade|Função|Objetivo|
|--|--|--|--|
|Simples|Location|origem|onde o plano de deixa de|
|Simples|Location|Destino|onde o plano que chegam|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exemplo de entidade não-machine-aprendidas de funções

Na expressão "Agendar a reunião de 8 a 9", ambos os números indicam um período de tempo, mas cada vez que tem um significado diferente na expressão. As funções fornecem o nome para as diferenças. 

```
Schedule the meeting from 8 to 9
```

|Tipo de entidade|Nome da função|Value|
|--|--|--|
|DatetimeV2 pré-criados|StartTime|8|
|DatetimeV2 pré-criados|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>São várias entidades numa expressão a mesma coisa que funções? 

Várias entidades podem existir numa expressão e podem ser extraídas sem utilizar as funções. Se o contexto da sentença indica com a versão da entidade tem um valor, em seguida, uma função deve ser utilizada. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Não utilize as funções para os duplicados sem significado

Se a expressão inclui uma lista de localizações, `I want to travel to Seattle, Cairo, and London.`, esta é uma lista em que cada item não têm um significado adicional. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Utilize as funções se duplicados indicarem significado

Se a expressão inclui uma lista de localizações com significado, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, este significado de origem, trânsito e de destino deve ser capturado com as funções.

### <a name="roles-can-indicate-order"></a>Funções podem indicar a ordem

Se a expressão foi alterada para indicar a ordem em que quisesse para extrair, `I want to first start with Seattle, second London, then third Cairo`, pode extrair de duas formas. Pode marcar os tokens que indicam a função `first start with`, `second`, `third`. Também pode usar a entidade pré-criados **Ordinal** e o **GeographyV2** pré-criados entidade numa entidade composta para capturar a idéia de ordem e local. 

## <a name="how-are-roles-used-in-example-utterances"></a>Como o funções são utilizadas em expressões de exemplo?

Quando uma entidade tiver uma função e a entidade é marcada numa expressão de exemplo, tem a opção de selecionar apenas a entidade ou selecionar a entidade e a função. 

As expressões de exemplo seguintes utilizam entidades e as funções:

|Vista de token|Vista de entidade|
|--|--|
|Tenho interesse em saber mais sobre **Seattle**|Estou interessado em saber mais sobre {Location}|
|Comprar um pedido de suporte de Seattle para nova York|Comprar uma permissão de {localização: Origin} para {localização: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Como o funções são usadas em padrões?
Na expressão de modelo de um padrão, as funções são usadas dentro da expressão: 

|Padrão com funções de entidade|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintaxe de função em padrões
A entidade e a função estão entre parênteses, `{}`. A entidade e a função são separados por vírgula. 

## <a name="entity-roles-versus-collaborator-roles"></a>Funções de entidade em comparação com as funções do funcionário

Funções de entidade aplicam-se ao modelo de dados da aplicação LUIS. [O funcionário](luis-concept-collaborator.md) funções aplicam-se para níveis de acesso de criação. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passos Seguintes

* Utilize um [tutorial prático](tutorial-entity-roles.md) com funções de entidade com entidades não aprendidas máquina
* Saiba como adicionar [funções](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
