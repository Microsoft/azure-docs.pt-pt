---
title: Gerenciar domínios de falha em conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como escolher o número correto de FDs ao criar um conjunto de dimensionamento de máquinas virtuais.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: f97c7e6971fb9c58a3f08959c00c84e64e160916
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871944"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Escolhendo o número correto de domínios de falha para o conjunto de dimensionamento de máquinas virtuais
Os conjuntos de dimensionamento de máquinas virtuais são criados com cinco domínios de falha, por padrão, em regiões do Azure sem zonas. Para as regiões que dão suporte à implantação zonal de conjuntos de dimensionamento de máquinas virtuais, o valor padrão da contagem de domínios de falha é 1 para cada uma das zonas. FD = 1 nesse caso significa que as instâncias de VM que pertencem ao conjunto de dimensionamento serão distribuídas em vários racks de forma mais adequada.

Você também pode considerar alinhar o número de domínios de falha do conjunto de dimensionamento com o número de domínios de falha Managed Disks. Esse alinhamento pode ajudar a evitar a perda de quorum se um domínio de falha Managed Disks inteiro falhar. A contagem FD pode ser definida como menor ou igual ao número de Managed Disks domínios de falha disponíveis em cada uma das regiões. Consulte este [documento](../virtual-machines/windows/manage-availability.md) para saber mais sobre o número de Managed disks domínios de falha por região.

## <a name="rest-api"></a>API REST
Você pode definir a propriedade `properties.platformFaultDomainCount` como 1, 2 ou 3 (o padrão de 5, se não for especificado). Consulte a documentação da API REST [aqui](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>CLI do Azure
Você pode definir o parâmetro `--platform-fault-domain-count` como 1, 2 ou 3 (o padrão de 5, se não for especificado). Consulte a documentação para CLI do Azure [aqui](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [recursos de disponibilidade e redundância](../virtual-machines/windows/availability.md) para ambientes do Azure.
