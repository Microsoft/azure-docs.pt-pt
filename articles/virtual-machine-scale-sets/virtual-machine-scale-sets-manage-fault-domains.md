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
ms.openlocfilehash: 32efde2cb2497fb8aab415d09a1063ff07c6b0f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87837198"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Escolher o número certo de domínios de falha para conjuntos de dimensionamento de máquinas virtuais
Os conjuntos de escala de máquina virtual são criados com cinco domínios de falha por padrão nas regiões de Azure sem zonas. Para as regiões que suportam a implantação zonal de conjuntos de escala de máquinas virtuais e esta opção é selecionada, o valor padrão da contagem de domínio de avaria é 1 para cada uma das zonas. FD=1 neste caso implica que os casos de VM pertencentes ao conjunto de escala serão espalhados por muitos racks numa melhor base de esforço.

Também pode considerar alinhar o número de domínios de falha definidos de escala com o número de domínios de falha de Discos Geridos. Este alinhamento pode ajudar a prevenir a perda de quórum se todo o domínio de falha dos Discos Geridos se avariar. A contagem de FD pode ser definida para menos ou igual ao número de domínios de falha de Discos Geridos disponíveis em cada uma das regiões. Consulte este [documento](../virtual-machines/windows/manage-availability.md) para saber sobre o número de domínios de falha de Discos Geridos por região.

## <a name="rest-api"></a>API REST
Pode definir a propriedade `properties.platformFaultDomainCount` para 1, 2 ou 3 (padrão de 3 se não for especificado). Consulte [aqui](/rest/api/compute/virtualmachinescalesets/createorupdate)a documentação para REST API .

## <a name="azure-cli"></a>CLI do Azure
Pode definir o parâmetro `--platform-fault-domain-count` para 1, 2 ou 3 (predefinição de 3 se não for especificado). Consulte [aqui](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)a documentação do Azure CLI .

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
- Saiba mais sobre [disponibilidade e recursos de redundância](../virtual-machines/availability.md) para ambientes Azure.