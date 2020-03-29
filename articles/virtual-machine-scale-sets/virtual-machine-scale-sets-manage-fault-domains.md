---
title: Gerir domínios de falha em conjuntos de escala de máquinas virtuais Azure
description: Aprenda a escolher o número certo de FDs enquanto cria um conjunto de escala de máquina virtual.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275733"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Escolher o número certo de domínios de falha para conjuntos de dimensionamento de máquinas virtuais
Os conjuntos de escala de máquinas virtuais são criados com cinco domínios de falha por padrão nas regiões de Azure sem zonas. Para as regiões que suportam a implantação zonal de conjuntos de escala de máquinas virtuais e esta opção é selecionada, o valor padrão da contagem de domínio de avaria é 1 para cada uma das zonas. FD=1 neste caso implica que os casos de VM pertencentes ao conjunto de escala serão distribuídos por muitas prateleiras com o melhor esforço.

Também pode considerar alinhar o número de domínios de falha definidos em escala com o número de domínios de falha de Discos Geridos. Este alinhamento pode ajudar a prevenir a perda de quórum se um domínio de falha de discos geridos inteiro descer. A contagem de FD pode ser definida como inferior ou igual ao número de domínios de falha de discos geridos disponíveis em cada uma das regiões. Consulte este [documento](../virtual-machines/windows/manage-availability.md) para saber sobre o número de domínios de falha de Discos Geridos por região.

## <a name="rest-api"></a>API REST
Pode definir a `properties.platformFaultDomainCount` propriedade para 1, 2 ou 3 (padrão de 5 se não especificado). Consulte a documentação para rest API [aqui](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>CLI do Azure
Pode definir o `--platform-fault-domain-count` parâmetro para 1, 2 ou 3 (padrão de 5, se não especificado). Consulte a documentação do Azure CLI [aqui](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

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

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre disponibilidade e recursos de [redundância](../virtual-machines/windows/availability.md) para ambientes Azure.
