---
title: Funções para entidades-LUIS
titleSuffix: Azure Cognitive Services
description: As funções são subtipos contextuais, com o nome de uma entidade usado apenas em padrões. Por exemplo, na expressão `buy a ticket from New York to London`, nova York e Londres cidades mas cada um tem um significado diferente da sentença. Nova Iorque é a cidade de origem e Londres é a cidade de destino.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b4bd61ea74055a04718d8a9d8d5ccd42671af2ac
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638345"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Funções de entidade para subtipos contextuais

As funções permitem que as entidades tenham os subtipos nomeados. Uma função pode ser usada com qualquer tipo de entidade predefinida ou personalizada e usada nos dois exemplos de declarações e padrões. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exemplo de funções da entidade aprendida por máquina

No expressão "comprar um tíquete de **Nova York** para **Londres**, a Nova York e a Londres são cidades, mas cada uma tem um significado diferente na sentença. Nova Iorque é a cidade de origem e Londres é a cidade de destino. 

```
buy a ticket from New York to London
```

As funções oferecem um nome para essas diferenças:

|Tipo de entidade|Nome da entidade|Role|Objetivo|
|--|--|--|--|
|Simples|Location|origem|onde o plano de deixa de|
|Simples|Location|Destino|onde o plano que chegam|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exemplo de funções de entidade não aprendida por máquina

No expressão "agendar a reunião de 8 a 9", os números indicam um tempo, mas cada vez tem um significado diferente no expressão. As funções fornecem o nome para as diferenças. 

```
Schedule the meeting from 8 to 9
```

|Tipo de entidade|Nome da função|Value|
|--|--|--|
|DatetimeV2 pré-criados|StartTime|8|
|DatetimeV2 pré-criados|Final|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Há várias entidades em um expressão da mesma forma que as funções? 

Várias entidades podem existir em um expressão e podem ser extraídas sem usar funções. Se o contexto da sentença indicar qual versão da entidade tem um valor, uma função deverá ser usada. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Não use funções para duplicatas sem significado

Se o expressão incluir uma lista de locais, `I want to travel to Seattle, Cairo, and London.`, essa será uma lista em que cada item não tem um significado adicional. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Usar funções se as duplicatas indicarem significado

Se o expressão incluir uma lista de locais com significado, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, esse significado de Origin, trânsito e Destination deverão ser capturados com funções.

### <a name="roles-can-indicate-order"></a>As funções podem indicar a ordem

Se o expressão for alterado para indicar a ordem que você deseja extrair `I want to first start with Seattle, second London, then third Cairo`, você poderá extrair de duas maneiras. Você pode marcar os tokens que indicam a função `first start with`, `second` `third`,,. Você também pode usar o **ordinal** da entidade predefinida e a entidade predefinida **GeographyV2** em uma entidade composta para capturar a ideia de Order e Place. 

## <a name="how-are-roles-used-in-example-utterances"></a>Como as funções são usadas no exemplo declarações?

Quando uma entidade tem uma função e a entidade é marcada em um exemplo expressão, você tem a opção de selecionar apenas a entidade ou selecionar a entidade e a função. 

O exemplo a seguir declarações usar entidades e funções:

|Exibição de token|Exibição de entidade|
|--|--|
|Sou interessante em saber mais sobre **Seattle**|Estou interessado em saber mais sobre {Location}|
|Comprar um tíquete de Seattle para Nova York|Compre um tíquete de {Location: Origin} para {Location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>Como o funções são usadas em padrões?
Na expressão de modelo de um padrão, as funções são usadas dentro da expressão: 

|Padrão com funções de entidade|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintaxe de função em padrões
A entidade e a função estão entre parênteses, `{}`. A entidade e a função são separados por vírgula. 

## <a name="entity-roles-versus-collaborator-roles"></a>Funções de entidade versus funções de colaborador

As funções de entidade se aplicam ao modelo de dados do aplicativo LUIS. As funções de [colaborador](luis-concept-collaborator.md) se aplicam a níveis de acesso de criação. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passos Seguintes

* Use um [tutorial prático](tutorial-entity-roles.md) usando funções de entidade com entidades não aprendidas por máquina
* Saiba como adicionar [funções](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
