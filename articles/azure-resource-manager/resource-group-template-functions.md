---
title: Funções do modelo
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para recuperar valores, trabalhar com cadeias de caracteres e numéricos e recuperar informações de implantação.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: cf263bff72677778433d4ef2f3cee8135fe3ab06
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224186"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager funções de modelo

Este artigo descreve todas as funções que você pode usar em um modelo de Azure Resource Manager. Para obter informações sobre como usar funções em seu modelo, consulte [sintaxe do modelo](template-expressions.md).

Para criar suas próprias funções, consulte [funções definidas pelo usuário](resource-group-authoring-templates.md#functions).

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
O Gerenciador de recursos fornece várias funções para trabalhar com matrizes e objetos.

* [variedade](resource-group-template-functions-array.md#array)
* [COALESCE](resource-group-template-functions-array.md#coalesce)
* [Concat](resource-group-template-functions-array.md#concat)
* [terá](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [esvaziá](resource-group-template-functions-array.md#empty)
* [primeiro](resource-group-template-functions-array.md#first)
* [interseção](resource-group-template-functions-array.md#intersection)
* [JSON](resource-group-template-functions-array.md#json)
* [última](resource-group-template-functions-array.md#last)
* [muito](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [maximizar](resource-group-template-functions-array.md#max)
* [amplitude](resource-group-template-functions-array.md#range)
* [saltar](resource-group-template-functions-array.md#skip)
* [ter](resource-group-template-functions-array.md#take)
* [unida](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Funções de comparação
O Gerenciador de recursos fornece várias funções para fazer comparações em seus modelos.

* [seja](resource-group-template-functions-comparison.md#equals)
* [inferiores](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [grande](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Funções de valor de implantação
O Gerenciador de recursos fornece as seguintes funções para obter valores de seções do modelo e valores relacionados à implantação:

* [planta](resource-group-template-functions-deployment.md#deployment)
* [ambiente](resource-group-template-functions-deployment.md#environment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [as](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Funções lógicas
O Gerenciador de recursos fornece as seguintes funções para trabalhar com condições lógicas:

* [e](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [que](resource-group-template-functions-logical.md#if)
* [válido](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

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
O Gerenciador de recursos fornece as seguintes funções para trabalhar com números inteiros:

* [agrega](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [marca](resource-group-template-functions-numeric.md#div)
* [barra](resource-group-template-functions-numeric.md#float)
* [inteiro](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [maximizar](resource-group-template-functions-numeric.md#max)
* [resto](resource-group-template-functions-numeric.md#mod)
* [Mul](resource-group-template-functions-numeric.md#mul)
* [projeto](resource-group-template-functions-numeric.md#sub)

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
O Resource Manager proporciona as seguintes funções para obter valores do recurso:

* [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)
* [listAccountSas](resource-group-template-functions-resource.md#list)
* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [lista](resource-group-template-functions-resource.md#list)
* [fornecedor](resource-group-template-functions-resource.md#providers)
* [referência](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [Identificação](resource-group-template-functions-resource.md#resourceid)
* [subscrição](resource-group-template-functions-resource.md#subscription)
* [subscriptionResourceId](resource-group-template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](resource-group-template-functions-resource.md#tenantresourceid)

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
O Gerenciador de recursos fornece as seguintes funções para trabalhar com cadeias de caracteres:

* [Base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [Concat](resource-group-template-functions-string.md#concat)
* [terá](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [esvaziá](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [primeiro](resource-group-template-functions-string.md#first)
* [ao](resource-group-template-functions-string.md#format)
* [volume](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [última](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [muito](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [PadLeft preenche](resource-group-template-functions-string.md#padleft)
* [Substitua](resource-group-template-functions-string.md#replace)
* [saltar](resource-group-template-functions-string.md#skip)
* [dividir](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [Strings](resource-group-template-functions-string.md#string)
* [Subcadeia](resource-group-template-functions-string.md#substring)
* [ter](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [Trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [URI](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [criando modelos de Azure Resource Manager](resource-group-authoring-templates.md)
* Para mesclar vários modelos, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md)
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md)
* Para ver como implantar o modelo que você criou, consulte [implantar um aplicativo com Azure Resource Manager modelo](resource-group-template-deploy.md)
