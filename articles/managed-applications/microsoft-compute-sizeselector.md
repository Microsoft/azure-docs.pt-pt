---
title: Elemento de interface do Usuário de SizeSelector do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Compute.SizeSelector para o portal do Azure.
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
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251317"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de interface do Usuário de Microsoft.Compute.SizeSelector
Um controlo para selecionar um tamanho para um ou mais instâncias de máquina virtual.

## <a name="ui-sample"></a>Exemplo de interface do Usuário

O utilizador vê um Seletor com valores predefinidos da definição de elemento.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Depois de selecionar o controle, o utilizador verá uma vista expandida dos tamanhos disponíveis.

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

## <a name="remarks"></a>Observações
- `recommendedSizes` deve ter, pelo menos, um tamanho. O primeiro tamanho recomendado é utilizado como predefinição. A lista de tamanhos disponíveis não está ordenada pelo Estado recomendado. O utilizador pode selecionar essa coluna para ordenar pelo Estado recomendado.
- Se um tamanho recomendado não estiver disponível na localização selecionada, o tamanho automaticamente será ignorado. Em vez disso, o próximo tamanho recomendado é utilizado.
- `constraints.allowedSizes` e `constraints.excludedSizes` são ambos opcionais, mas não pode ser utilizada em simultâneo. A lista de tamanhos disponíveis pode ser determinada ao chamar [listar tamanhos de máquina virtual de disponibilidade para uma subscrição](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Qualquer tamanho não especificado na `constraints.allowedSizes` está oculta e qualquer tamanho não especificado no `constraints.excludedSizes` é mostrado.
- `osPlatform` tem de ser especificado e pode ser uma **Windows** ou **Linux**. É utilizado para determinar os custos de hardware das máquinas virtuais.
- `imageReference` é omitido para imagens originais, mas fornecido para imagens de terceiros. É utilizado para determinar os custos de software das máquinas virtuais.
- `count` é utilizado para definir o multiplicador apropriado para o elemento. Ele oferece suporte a um valor estático, como **2**, ou um valor dinâmico a partir de outro elemento, como `[steps('step1').vmCount]`. O valor predefinido é **1**.
- O `numAvailabilityZonesRequired` pode ser 1, 2 ou 3.
- Por predefinição, `hideDiskTypeFilter` é **false**. O filtro de tipo de disco permite ao utilizador ver todos os tipos de disco ou apenas SSD.

## <a name="sample-output"></a>Resultado da amostra
```json
"Standard_D1"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
