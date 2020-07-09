---
title: Gerir domínios de avaria em conjuntos de escala de máquina virtual Azure
description: Aprenda a escolher o número certo de FDs enquanto cria um conjunto de escala de máquina virtual.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: f1d8bad3d0d3e73a387bdc76131461e5061bafce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263182"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Escolher o número certo de domínios de falha para conjuntos de dimensionamento de máquinas virtuais
Os conjuntos de escala de máquina virtual são criados com cinco domínios de falha por padrão nas regiões de Azure sem zonas. Para as regiões que suportam a implantação zonal de conjuntos de escala de máquinas virtuais e esta opção é selecionada, o valor padrão da contagem de domínio de avaria é 1 para cada uma das zonas. FD=1 neste caso implica que os casos de VM pertencentes ao conjunto de escala serão espalhados por muitos racks numa melhor base de esforço.

Também pode considerar alinhar o número de domínios de falha definidos de escala com o número de domínios de falha de Discos Geridos. Este alinhamento pode ajudar a prevenir a perda de quórum se todo o domínio de falha dos Discos Geridos se avariar. A contagem de FD pode ser definida para menos ou igual ao número de domínios de falha de Discos Geridos disponíveis em cada uma das regiões. Consulte este [documento](../virtual-machines/windows/manage-availability.md) para saber sobre o número de domínios de falha de Discos Geridos por região.

## <a name="rest-api"></a>API REST
Pode definir a propriedade `properties.platformFaultDomainCount` para 1, 2 ou 3 (padrão de 3 se não for especificado). Consulte [aqui](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)a documentação para REST API .

## <a name="azure-cli"></a>CLI do Azure
Pode definir o parâmetro `--platform-fault-domain-count` para 1, 2 ou 3 (predefinição de 3 se não for especificado). Consulte [aqui](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)a documentação do Azure CLI .

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

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre [disponibilidade e recursos de redundância](../virtual-machines/windows/availability.md) para ambientes Azure.
