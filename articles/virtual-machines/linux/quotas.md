---
title: quotas de vCPU para o Azure | Documentos da Microsoft
description: Saiba mais sobre as quotas de vCPU para o Azure.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 40fd91ed39731b95cf01a627e3178ab6cfaaa7d3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670992"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As quotas de vCPU para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são dispostas em duas camadas para cada subscrição, em cada região. A primeira camada é o Total Regional vCPUs e a segunda camada é os vários tamanho famílias núcleos de VMS, como o vCPUs de série D. Sempre que uma nova VM é implementada a vCPUs para a VM não pode exceder a quota de vCPU para a família de tamanho VM ou a quota de total regional vCPU. Se qualquer um das quotas são excedido, a implementação da VM será não ser permitida. Também existe uma quota para o número geral de máquinas virtuais na região. Os detalhes sobre cada uma destas quotas podem ser vistos na **utilização + quotas** secção a **subscrição** página no [portal do Azure](https://portal.azure.com), ou pode consultar os valores a utilizar o Azure CLI.


## <a name="check-usage"></a>Verificar utilização

Pode verificar a sua utilização de quota através de [az vm lista-utilização](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

O resultado deverá ser semelhante ao seguinte:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Instâncias de VM Reservada
Instâncias de VM reservadas, que estão confinadas a uma subscrição individual sem flexibilidade de tamanho VM, adicionará um novo aspecto as quotas de vCPU. Estes valores descrevem o número de instâncias do tamanho declarado que tem de ser implementável na subscrição. Funcionam como um marcador de posição no sistema de quota para se certificar de que a quota é reservado para garantir que as reservas do Azure são implementáveis na subscrição. Por exemplo, se uma subscrição específica tem 10 Standard_D1 reservas o limite de utilizações para reservas Standard_D1 será 10. Isso fará com que o Azure garantir que sempre há pelo menos 10 vCPUs disponíveis na quota de vCPUs regionais Total a ser utilizado para Standard_D1 instâncias e existem, pelo menos, 10 vCPUs disponíveis na quota de vCPU de família de D Standard a ser utilizado para instâncias de Standard_D1.

Se um aumento de quota é necessário adquirir uma RI único de subscrição, pode [pedir um aumento de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) na sua subscrição.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre faturação e de quotas, consulte [subscrição do Azure e limites do serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
