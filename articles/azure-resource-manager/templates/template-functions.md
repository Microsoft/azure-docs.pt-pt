---
title: Funções de modelos
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para recuperar valores, trabalhar com cordas e numéricos e recuperar informações de implantação.
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: dbda5c635a5189158424363c341b269d331a5fdc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707324"
---
# <a name="arm-template-functions"></a>Funções do modelo ARM

Este artigo descreve todas as funções que pode utilizar num modelo de Gestor de Recursos Azure (modelo ARM). Para obter informações sobre a utilização de funções no seu modelo, consulte [a sintaxe do modelo](template-expressions.md).

Para criar as suas próprias funções, consulte [as funções definidas pelo Utilizador](template-syntax.md#functions).

A maioria das funções funciona da mesma forma quando implantada num grupo de recursos, subscrição, grupo de gestão ou inquilino. Algumas funções não podem ser usadas em todos os âmbitos. Estão anotados nas listas abaixo.

<a id="array" aria-hidden="true"></a>
<a id="concatarray" aria-hidden="true"></a>
<a id="contains" aria-hidden="true"></a>
<a id="createarray" aria-hidden="true"></a>
<a id="empty" aria-hidden="true"></a>
<a id="first" aria-hidden="true"></a>
<a id="intersection" aria-hidden="true"></a>
<a id="last" aria-hidden="true"></a>
<a id="length" aria-hidden="true"></a>
<a id="min" aria-hidden="true"></a>
<a id="max" aria-hidden="true"></a>
<a id="range" aria-hidden="true"></a>
<a id="skip" aria-hidden="true"></a>
<a id="take" aria-hidden="true"></a>
<a id="union" aria-hidden="true"></a>

## <a name="any-function"></a>Qualquer função

Qualquer [função](template-functions-any.md) está disponível no Bicep para ajudar a resolver problemas em torno de avisos de tipo de dados.

## <a name="array-functions"></a>Funções de matriz

O Gestor de Recursos fornece várias funções para trabalhar com matrizes.

* [matriz](template-functions-array.md#array)
* [concat](template-functions-array.md#concat)
* [contém](template-functions-array.md#contains)
* [criarArray](template-functions-array.md#createarray)
* [vazio](template-functions-array.md#empty)
* [primeiro](template-functions-array.md#first)
* [intersecção](template-functions-array.md#intersection)
* [último](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [min](template-functions-array.md#min)
* [máx](template-functions-array.md#max)
* [gama](template-functions-array.md#range)
* [saltar](template-functions-array.md#skip)
* [tomar](template-functions-array.md#take)
* [união](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true"></a>
<a id="equals" aria-hidden="true"></a>
<a id="less" aria-hidden="true"></a>
<a id="lessorequals" aria-hidden="true"></a>
<a id="greater" aria-hidden="true"></a>
<a id="greaterorequals" aria-hidden="true"></a>

## <a name="comparison-functions"></a>Funções de comparação

O Gestor de Recursos fornece várias funções para fazer comparações nos seus modelos.

* [coalesce](template-functions-comparison.md#coalesce)
* [é igual a](template-functions-comparison.md#equals)
* [menos](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true"></a>
<a id="parameters" aria-hidden="true"></a>
<a id="variables" aria-hidden="true"></a>

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

<a id="and" aria-hidden="true"></a>
<a id="bool" aria-hidden="true"></a>
<a id="if" aria-hidden="true"></a>
<a id="not" aria-hidden="true"></a>
<a id="or" aria-hidden="true"></a>

## <a name="logical-functions"></a>Funções lógicas

O Gestor de Recursos fornece as seguintes funções para trabalhar com condições lógicas:

* [and](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [falso](template-functions-logical.md#false)
* [se](template-functions-logical.md#if)
* [não](template-functions-logical.md#not)
* [ou](template-functions-logical.md#or)
* [verdade](template-functions-logical.md#true)

<a id="add" aria-hidden="true"></a>
<a id="copyindex" aria-hidden="true"></a>
<a id="div" aria-hidden="true"></a>
<a id="float" aria-hidden="true"></a>
<a id="int" aria-hidden="true"></a>
<a id="minint" aria-hidden="true"></a>
<a id="maxint" aria-hidden="true"></a>
<a id="mod" aria-hidden="true"></a>
<a id="mul" aria-hidden="true"></a>
<a id="sub" aria-hidden="true"></a>

## <a name="numeric-functions"></a>Funções numéricas

O Gestor de Recursos fornece as seguintes funções para trabalhar com inteiros:

* [adicionar](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [flutuante](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [min](template-functions-numeric.md#min)
* [máx](template-functions-numeric.md#max)
* [mod](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true"></a>

## <a name="object-functions"></a>Funções de objeto

O Gestor de Recursos fornece várias funções para trabalhar com objetos.

* [contém](template-functions-object.md#contains)
* [criarObject](template-functions-object.md#createobject)
* [vazio](template-functions-object.md#empty)
* [intersecção](template-functions-object.md#intersection)
* [json](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [nulo](template-functions-object.md#null)
* [união](template-functions-object.md#union)

<a id="extensionResourceId" aria-hidden="true"></a>
<a id="listkeys" aria-hidden="true"></a>
<a id="list" aria-hidden="true"></a>
<a id="providers" aria-hidden="true"></a>
<a id="reference" aria-hidden="true"></a>
<a id="resourcegroup" aria-hidden="true"></a>
<a id="resourceid" aria-hidden="true"></a>
<a id="subscription" aria-hidden="true"></a>
<a id="subscriptionResourceId" aria-hidden="true"></a>
<a id="tenantResourceId" aria-hidden="true"></a>

## <a name="resource-functions"></a>Funções de recursos

O Gestor de Recursos fornece as seguintes funções para obter valores de recursos:

* [extensãoResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [lista*](template-functions-resource.md#list)
* [pickZones](template-functions-resource.md#pickzones)
* [fornecedores](template-functions-resource.md#providers)
* [referência](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - só pode ser utilizado em implementações para um grupo de recursos.
* [resourceId](template-functions-resource.md#resourceid) - pode ser usado em qualquer âmbito, mas os parâmetros válidos mudam dependendo do âmbito.
* [subscrição](template-functions-resource.md#subscription) - só pode ser usada em implementações para um grupo de recursos ou subscrição.
* [subscriçãoResourceId](template-functions-resource.md#subscriptionresourceid)
* [inquilinoResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true"></a>
<a id="base64tojson" aria-hidden="true"></a>
<a id="base64tostring" aria-hidden="true"></a>
<a id="concat" aria-hidden="true"></a>
<a id="containsstring" aria-hidden="true"></a>
<a id="datauri" aria-hidden="true"></a>
<a id="datauritostring" aria-hidden="true"></a>
<a id="emptystring" aria-hidden="true"></a>
<a id="endswith" aria-hidden="true"></a>
<a id="firststring" aria-hidden="true"></a>
<a id="guid" aria-hidden="true"></a>
<a id="indexof" aria-hidden="true"></a>
<a id="laststring" aria-hidden="true"></a>
<a id="lastindexof" aria-hidden="true"></a>
<a id="lengthstring" aria-hidden="true"></a>
<a id="padleft" aria-hidden="true"></a>
<a id="replace" aria-hidden="true"></a>
<a id="skipstring" aria-hidden="true"></a>
<a id="split" aria-hidden="true"></a>
<a id="startswith" aria-hidden="true"></a>
<a id="string" aria-hidden="true"></a>
<a id="substring" aria-hidden="true"></a>
<a id="takestring" aria-hidden="true"></a>
<a id="tolower" aria-hidden="true"></a>
<a id="toupper" aria-hidden="true"></a>
<a id="trim" aria-hidden="true"></a>
<a id="uniquestring" aria-hidden="true"></a>
<a id="uri" aria-hidden="true"></a>
<a id="uricomponent" aria-hidden="true"></a>
<a id="uricomponenttostring" aria-hidden="true"></a>

## <a name="string-functions"></a>Funções de cadeia

O Gestor de Recursos fornece as seguintes funções para trabalhar com cordas:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [contém](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [vazio](template-functions-string.md#empty)
* [terminaWith](template-functions-string.md#endswith)
* [primeiro](template-functions-string.md#first)
* [formato](template-functions-string.md#format)
* [guid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [último](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [substituir](template-functions-string.md#replace)
* [saltar](template-functions-string.md#skip)
* [divisão](template-functions-string.md#split)
* [começacom](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [substring](template-functions-string.md#substring)
* [tomar](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toupper](template-functions-string.md#toupper)
* [guarnição](template-functions-string.md#trim)
* [exclusivosastragem](template-functions-string.md#uniquestring)
* [uri](template-functions-string.md#uri)
* [uriComponente](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para fundir vários modelos, consulte [utilizando modelos ligados e aninhados ao implementar recursos Azure](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte a iteração de [recursos nos modelos ARM](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [recursos de implantação com modelos ARM e Azure PowerShell](deploy-powershell.md).
