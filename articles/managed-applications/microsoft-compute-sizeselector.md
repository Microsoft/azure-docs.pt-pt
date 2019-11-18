---
title: Elemento de interface do usuário SizeSelector do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. COMPUTE. SizeSelector para portal do Azure. Use para selecionar o tamanho de uma máquina virtual.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: f3a8426856e1345306acff69946beb4860d5f905
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151527"
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
