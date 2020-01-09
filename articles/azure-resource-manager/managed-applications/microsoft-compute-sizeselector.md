---
title: Elemento de interface do usuário SizeSelector
description: Descreve o elemento de interface do usuário Microsoft. COMPUTE. SizeSelector para portal do Azure. Use para selecionar o tamanho de uma máquina virtual.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652050"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de interface do usuário Microsoft. COMPUTE. SizeSelector

Um controle para selecionar um tamanho para uma ou mais instâncias de máquina virtual.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O usuário vê um seletor com valores padrão da definição do elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Depois de selecionar o controle, o usuário verá uma exibição expandida dos tamanhos disponíveis.

![Microsoft. COMPUTE. SizeSelector expandido](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

- `recommendedSizes` deve ter pelo menos um tamanho. O primeiro tamanho recomendado é usado como o padrão. A lista de tamanhos disponíveis não é classificada pelo Estado recomendado. O usuário pode selecionar essa coluna para classificar por estado recomendado.
- Se um tamanho recomendado não estiver disponível no local selecionado, o tamanho será ignorado automaticamente. Em vez disso, o próximo tamanho recomendado é usado.
- `constraints.allowedSizes` e `constraints.excludedSizes` são opcionais, mas não podem ser usados simultaneamente. A lista de tamanhos disponíveis pode ser determinada pela chamada à [lista de tamanhos de máquina virtual disponíveis para uma assinatura](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Qualquer tamanho não especificado na `constraints.allowedSizes` é ocultado e qualquer tamanho não especificado em `constraints.excludedSizes` é mostrado.
- `osPlatform` deve ser especificado e pode ser **Windows** ou **Linux**. Ele é usado para determinar os custos de hardware das máquinas virtuais.
- `imageReference` é omitido para imagens de terceiros, mas fornecida para imagens de terceiros. Ele é usado para determinar os custos de software das máquinas virtuais.
- `count` é usado para definir o multiplicador apropriado para o elemento. Ele dá suporte a um valor estático, como **2**, ou a um valor dinâmico de outro elemento, como `[steps('step1').vmCount]`. O valor padrão é **1**.
- O `numAvailabilityZonesRequired` pode ser 1, 2 ou 3.
- Por padrão, `hideDiskTypeFilter` é **false**. O filtro de tipo de disco permite que o usuário veja todos os tipos de disco ou apenas SSD.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
