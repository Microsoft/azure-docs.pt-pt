---
title: Criar funções matemáticas de definição de UI
description: Descreve as funções a utilizar na realização de operações matemáticas.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098171"
---
# <a name="createuidefinition-math-functions"></a>Funções matemáticas CreateUiDefinition

As funções permitem-lhe realizar operações matemáticas.

## <a name="add"></a>adicionar

Adiciona dois números e devolve o resultado.

O seguinte exemplo `3` regressa:

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

Devolve o maior número inteiro maior ou igual ao número especificado.

O seguinte exemplo `4` regressa:

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Divide o primeiro número pelo segundo número e devolve o resultado. O resultado é sempre um inteiro.

O seguinte exemplo `2` regressa:

```json
"[div(6, 3)]"
```

## <a name="floor"></a>chão

Devolve o maior número inteiro menos ou igual ao número especificado.

O seguinte exemplo `3` regressa:

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Devolve o maior dos dois números.

O seguinte exemplo `2` regressa:

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Devolve o menor dos dois números.

O seguinte exemplo `1` regressa:

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

Divide o primeiro número pelo segundo número, e devolve o restante.

O seguinte exemplo `0` regressa:

```json
"[mod(6, 3)]"
```

O seguinte exemplo `2` regressa:

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Multiplica dois números e devolve o resultado.

O seguinte exemplo `6` regressa:

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

Devolve um número integral aleatório dentro do intervalo especificado. Esta função não gera números aleatórios criptograficamente seguros.

O seguinte exemplo poderia `42` voltar:

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>gama

Gera uma sequência de números integrais dentro do intervalo especificado.

O seguinte exemplo `[1,2,3]` regressa:

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

Subtrai o segundo número do primeiro número e devolve o resultado.

O seguinte exemplo `1` regressa:

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../management/overview.md).
