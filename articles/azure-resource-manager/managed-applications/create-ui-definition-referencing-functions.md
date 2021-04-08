---
title: Criar funções de referência de definição de UI
description: Descreve as funções a utilizar ao construir definições de UI para o portal Azure que referenciam outros objetos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098174"
---
# <a name="createuidefinition-referencing-functions"></a>Criar Funções de referência createUiDefinition

As funções a utilizar quando referenciar saídas a partir das propriedades ou contexto de um ficheiro CreateUiDefinition.

## <a name="basics"></a>Básico

Devolve os valores de saída de um elemento que é definido na etapa [Básico.](create-uidefinition-overview.md#basics) Passe em nome do elemento como parâmetro para esta função.

Para obter os valores de saída dos elementos em outras etapas, utilize a função [passos().](#steps)

O exemplo a seguir devolve a saída do elemento nomeado `clusterName` na etapa Basics:

```json
"[basics('clusterName')]"
```

Os valores devolvidos variam em função do tipo de elemento que é recuperado.

## <a name="location"></a>localização

Devolve o local selecionado na etapa Básica ou no contexto atual.

O exemplo a seguir devolve um valor `"westus"` como:

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Devolve detalhes sobre os recursos O Grupo selecionado na etapa Basics ou no contexto atual.

O seguinte exemplo:

```json
"[resourceGroup()]"
```

Devolve as seguintes propriedades:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Você pode obter qualquer valor particular com notação de ponto.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>passos

Devolve os elementos num passo especificado. Passe em nome do passo como parâmetro para esta função. A partir dos elementos devolvidos, você pode obter valores de propriedade particulares.

Para obter os valores de saída dos elementos na etapa Basics, utilize a função [básica().](#basics)

O exemplo a seguir devolve o passo denominado `vmParameters` . Nesse passo está um elemento chamado `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subscrição

Devolução de propriedades para a subscrição selecionada na etapa Basics ou no contexto atual.

O seguinte exemplo:

```json
"[subscription()]"
```

Devolve as seguintes propriedades:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução ao desenvolvimento da interface do portal, consulte [CreateUiDefinition.jspara a experiência de criação da aplicação gerida pelo Azure.](create-uidefinition-overview.md)
