---
title: Criar funções de definição de UI
description: Descreve as funções a utilizar na construção de definições de UI para aplicações geridas azure
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040990"
---
# <a name="createuidefinition-functions"></a>Funções createUiDefinition

Este artigo fornece uma visão geral das funções suportadas para CreateUiDefinition.

## <a name="function-syntax"></a>Sintaxe de função

Para utilizar uma função, rodeie a invocação com suportes quadrados. Por exemplo:

```json
"[function()]"
```

Cordas e outras funções podem ser referenciadas como parâmetros para uma função, mas as cordas devem ser cercadas em citações únicas. Por exemplo:

```json
"[fn1(fn2(), 'demo text')]"
```

Se for caso disso, pode referenciar propriedades da saída de uma função utilizando o operador de pontos. Por exemplo:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Funções de coleção

Estas funções podem ser usadas com coleções, como cordas JSON, matrizes e objetos.

* [contém](create-ui-definition-collection-functions.md#contains)
* [vazio](create-ui-definition-collection-functions.md#empty)
* [filtro](create-ui-definition-collection-functions.md#filter)
* [primeiro](create-ui-definition-collection-functions.md#first)
* [último](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [mapa](create-ui-definition-collection-functions.md#map)
* [saltar](create-ui-definition-collection-functions.md#skip)
* [divisão](create-ui-definition-collection-functions.md#split)
* [tomar](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Funções de conversão

Estas funções podem ser utilizadas para converter valores entre tipos de dados JSON e codificações.

* [bool](create-ui-definition-conversion-functions.md#bool)
* [descodificarBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [codificarBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [flutuante](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [string](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Funções de data

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Funções lógicas

Estas funções podem ser utilizadas em condicional. Algumas funções podem não suportar todos os tipos de dados JSON.

* [and](create-ui-definition-logical-functions.md#and)
* [coalesce](create-ui-definition-logical-functions.md#coalesce)
* [é igual a](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [se](create-ui-definition-logical-functions.md#if)
* [menos](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [não](create-ui-definition-logical-functions.md#not)
* [ou](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Funções matemáticas

* [adicionar](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [chão](create-ui-definition-math-functions.md#floor)
* [máx](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [mod](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [rand](create-ui-definition-math-functions.md#rand)
* [gama](create-ui-definition-math-functions.md#range)
* [sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Funções de referência

* [Básico](create-ui-definition-referencing-functions.md#basics)
* [localização](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [passos](create-ui-definition-referencing-functions.md#steps)
* [subscrição](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Funções de cadeia

* [concat](create-ui-definition-string-functions.md#concat)
* [terminaWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [substituir](create-ui-definition-string-functions.md#replace)
* [começacom](create-ui-definition-string-functions.md#startswith)
* [substring](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toupper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
