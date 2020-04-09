---
title: Funções de modelos
description: Descreve as funções a utilizar num modelo do Gestor de Recursos Azure para recuperar valores, trabalhar com cordas e numéricos e recuperar informações de implementação.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982383"
---
# <a name="arm-template-functions"></a>Funções do modelo ARM

Este artigo descreve todas as funções que pode utilizar num modelo de Gestor de Recursos Azure (ARM). Para obter informações sobre a utilização de funções no seu modelo, consulte a [sintaxe](template-expressions.md)do modelo .

Para criar as suas próprias funções, consulte [funções definidas pelo Utilizador](template-syntax.md#functions).

A maioria das funções funciona da mesma forma quando implantada num grupo de recursos, subscrição, grupo de gestão ou inquilino. Algumas funções não podem ser usadas em todos os âmbitos. Estão anotados nas listas abaixo.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Funções de matriz e objeto

O Gestor de Recursos fornece várias funções para trabalhar com matrizes e objetos.

* [array](template-functions-array.md#array)
* [coalesce](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [contém](template-functions-array.md#contains)
* [criar Array](template-functions-array.md#createarray)
* [vazio](template-functions-array.md#empty)
* [primeiro](template-functions-array.md#first)
* [cruzamento](template-functions-array.md#intersection)
* [json](template-functions-array.md#json)
* [última](template-functions-array.md#last)
* [comprimento](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [máximo](template-functions-array.md#max)
* [alcance](template-functions-array.md#range)
* [saltar](template-functions-array.md#skip)
* [tomar](template-functions-array.md#take)
* [união](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funções de comparação

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos.

* [iguais](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Funções de data

O Gestor de Recursos fornece as seguintes funções para trabalhar com datas.

* [dataTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funções de valor de implantação

O Gestor de Recursos fornece as seguintes funções para obter valores a partir de secções do modelo e valores relacionados com a implementação:

* [implantação](template-functions-deployment.md#deployment)
* [ambiente](template-functions-deployment.md#environment)
* [parâmetros](template-functions-deployment.md#parameters)
* [variáveis](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funções lógicas

O Gestor de Recursos fornece as seguintes funções para trabalhar com condições lógicas:

* [e](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [se](template-functions-logical.md#if)
* [não](template-functions-logical.md#not)
* [ou](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Funções numéricas

O Gestor de Recursos fornece as seguintes funções para trabalhar com os inteiros:

* [adicionar](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [máximo](template-functions-numeric.md#max)
* [mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Funções de recursos

O Gestor de Recursos fornece as seguintes funções para obter valores de recursos:

* [extensãoResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listaKeys](template-functions-resource.md#listkeys)
* [listaSegredos](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [fornecedores](template-functions-resource.md#providers)
* [referência](template-functions-resource.md#reference)
* [recursosGroup](template-functions-resource.md#resourcegroup) - só pode ser utilizado em implantações para um grupo de recursos.
* [recursosId](template-functions-resource.md#resourceid) - pode ser usado em qualquer âmbito, mas os parâmetros válidos mudam dependendo do âmbito.
* [subscrição](template-functions-resource.md#subscription) - só pode ser utilizada em implantações para um grupo de recursos ou subscrição.
* [subscriçãoResourceId](template-functions-resource.md#subscriptionresourceid)
* [inquilinoResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Funções de cadeia

O Gestor de Recursos fornece as seguintes funções para trabalhar com cordas:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64Tostring](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [contém](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [datauritostring](template-functions-string.md#datauritostring)
* [vazio](template-functions-string.md#empty)
* [terminaCom](template-functions-string.md#endswith)
* [primeiro](template-functions-string.md#first)
* [formato](template-functions-string.md#format)
* [guia](template-functions-string.md#guid)
* [indexof](template-functions-string.md#indexof)
* [última](template-functions-string.md#last)
* [últimoIndexOf](template-functions-string.md#lastindexof)
* [comprimento](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [substituir](template-functions-string.md#replace)
* [saltar](template-functions-string.md#skip)
* [dividir](template-functions-string.md#split)
* [começaCom](template-functions-string.md#startswith)
* [cadeia](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [tomar](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [aparar](template-functions-string.md#trim)
* [único String](template-functions-string.md#uniquestring)
* [uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Passos seguintes

* Para uma descrição das secções num modelo ARM, consulte [os modelos ARM de autoria](template-syntax.md)
* Para fundir vários modelos, consulte [Usar modelos ligados com o Gestor de Recursos Azure](linked-templates.md)
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [Implementar uma aplicação com modelos ARM](deploy-powershell.md)
