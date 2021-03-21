---
title: Funções do modelo - qualquer
description: Descreve qualquer função disponível em Bicep para converter tipos.
ms.topic: conceptual
author: tfitzmac
ms.author: tomfitz
ms.service: azure-resource-manager
ms.subservice: templates
ms.date: 03/02/2021
ms.openlocfilehash: b0cb51c9a79d1100de7f1ef32fe326eddcdd6dcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746918"
---
# <a name="any-function-for-bicep"></a>Qualquer função para Bicep

O Bicep suporta uma função chamada `any()` para resolver erros de tipo no sistema do tipo Bicep. Utiliza esta função quando o formato do valor que fornece não corresponde ao que o sistema tipo espera. Por exemplo, se a propriedade necessitar de um número, mas você precisa fornecê-lo como uma corda, como `'0.5'` . Utilize a `any()` função para suprimir o erro reportado pelo sistema de tipo.

Esta função não existe no tempo de funcionamento do modelo Azure Resource Manager. É usado apenas por Bicep e não é emitido no JSON para o modelo construído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="any"></a>qualquer

`any(value)`

Devolve um valor compatível com qualquer tipo de dado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| valor | Yes | todos os tipos | O valor a converter para um tipo compatível. |

### <a name="return-value"></a>Valor devolvido

O valor de uma forma compatível com qualquer tipo de dado.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra como usar a `any()` função para fornecer valores numéricos como cordas.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

A função funciona em qualquer valor atribuído em Bicep. O exemplo a seguir usa `any()` com uma expressão ternuária como argumento.  

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>Passos seguintes

Para utilizações mais complexas da `any()` função, consulte os seguintes exemplos:

* [Recursos infantis que requerem nomes específicos](https://github.com/Azure/bicep/blob/main/docs/examples/201/api-management-create-all-resources/main.bicep#L246)
* [Uma propriedade de recursos não definida no tipo do recurso, mesmo que exista](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

