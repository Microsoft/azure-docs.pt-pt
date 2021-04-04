---
title: TamanhoSelector elemento UI
description: Descreve o elemento UI microsoft.compute.SizeSelector uI para o portal Azure. Utilizar para selecionar o tamanho de uma máquina virtual.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063617"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI elemento

Um controlo para selecionar um tamanho para uma ou mais instâncias de máquinas virtuais.

## <a name="ui-sample"></a>Amostra de UI

O utilizador vê um seletor com valores predefinidos a partir da definição de elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

Depois de selecionar o controlo, o utilizador vê uma visão expandida dos tamanhos disponíveis.

![Microsoft.Compute.SizeSelector expandido](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

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

## <a name="sample-output"></a>Saída de exemplo

```json
"Standard_D1"
```

## <a name="remarks"></a>Observações

- `recommendedSizes` deve ter pelo menos um tamanho. O primeiro tamanho recomendado é usado como padrão. A lista de tamanhos disponíveis não é ordenada pelo estado recomendado. O utilizador pode selecionar esta coluna para ordenar por estado recomendado.
- Se um tamanho recomendado não estiver disponível no local selecionado, o tamanho é automaticamente ignorado. Em vez disso, o próximo tamanho recomendado é usado.
- `constraints.allowedSizes` e `constraints.excludedSizes` são ambos opcionais, mas não podem ser usados simultaneamente. A lista de tamanhos disponíveis pode ser determinada através da chamada [Lista de tamanhos de máquinas virtuais disponíveis para uma subscrição.](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region) Qualquer tamanho não especificado no `constraints.allowedSizes` é escondido, e qualquer tamanho não especificado `constraints.excludedSizes` é mostrado.
- `osPlatform` deve ser especificado, e pode ser **windows** ou **Linux**. É usado para determinar os custos de hardware das máquinas virtuais.
- `imageReference` é omitida para imagens de primeira parte, mas fornecidas para imagens de terceiros. É usado para determinar os custos de software das máquinas virtuais.
- `count` é utilizado para definir o multiplicador apropriado para o elemento. Suporta um valor estático, como **2,** ou um valor dinâmico de outro elemento, como `[steps('step1').vmCount]` . O valor predefinido é **1**.
- A `numAvailabilityZonesRequired` lata é 1, 2 ou 3.
- Por defeito, `hideDiskTypeFilter` é **falso**. O filtro do tipo disco permite ao utilizador ver todos os tipos de discos ou apenas SSD.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
