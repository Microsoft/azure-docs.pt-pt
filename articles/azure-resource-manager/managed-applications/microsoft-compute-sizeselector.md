---
title: Elemento UI do Seletor tamanho
description: Descreve o elemento UI Microsoft.Compute.SizeSelector para o portal Azure. Utilize para selecionar o tamanho de uma máquina virtual.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652050"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI

Um controlo para selecionar um tamanho para uma ou mais situações de máquina virtual.

## <a name="ui-sample"></a>Amostra de UI

O utilizador vê um seletor com valores predefinidos a partir da definição do elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Depois de selecionar o controlo, o utilizador vê uma visão expandida dos tamanhos disponíveis.

![Microsoft.Compute.SizeSelector expandido](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"Standard_D1"
```

## <a name="remarks"></a>Observações

- `recommendedSizes`deve ter pelo menos um tamanho. O primeiro tamanho recomendado é usado como padrão. A lista de tamanhos disponíveis não é classificada pelo estado recomendado. O utilizador pode selecionar essa coluna para ordenar por estado recomendado.
- Se um tamanho recomendado não estiver disponível no local selecionado, o tamanho é automaticamente ignorado. Em vez disso, o próximo tamanho recomendado é usado.
- `constraints.allowedSizes`e `constraints.excludedSizes` são ambos opcionais, mas não podem ser usados simultaneamente. A lista de tamanhos disponíveis pode ser determinada através da [chamada Lista de tamanhos de máquina virtual disponíveis para uma subscrição](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Qualquer tamanho não especificado no `constraints.allowedSizes` está escondido, e `constraints.excludedSizes` qualquer tamanho não especificado é mostrado.
- `osPlatform`deve ser especificado e pode ser **Windows** ou **Linux**. É usado para determinar os custos de hardware das máquinas virtuais.
- `imageReference`é omitida para imagens de primeira parte, mas fornecida para imagens de terceiros. É usado para determinar os custos de software das máquinas virtuais.
- `count`é utilizado para definir o multiplicador apropriado para o elemento. Suporta um valor estático, como **2,** ou um `[steps('step1').vmCount]`valor dinâmico de outro elemento, como . O valor predefinido é **de 1**.
- Pode `numAvailabilityZonesRequired` ser 1, 2 ou 3.
- Por defeito, `hideDiskTypeFilter` é **falso.** O filtro do tipo disco permite ao utilizador ver todos os tipos de disco ou apenas SSD.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
