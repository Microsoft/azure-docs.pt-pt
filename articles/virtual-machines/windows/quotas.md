---
title: quotas de vCPU para o Azure | Documentos da Microsoft
description: Saiba mais sobre as quotas de vCPU para o Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: aa6e93ef7e403f1b230814b7afa20d2e8e68b71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718663"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As quotas de vCPU para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são dispostas em duas camadas para cada subscrição, em cada região. A primeira camada é o Total Regional vCPUs e a segunda camada é os vários tamanho famílias núcleos de VMS, como o vCPUs de série D. Sempre que uma nova VM é implementada a vCPUs para a VM não pode exceder a quota de vCPU para a família de tamanho VM ou a quota de total regional vCPU. Se qualquer um das quotas são excedido, a implementação da VM será não ser permitida. Também existe uma quota para o número geral de máquinas virtuais na região. Os detalhes sobre cada uma destas quotas podem ser vistos na **utilização + quotas** secção a **subscrição** página no [portal do Azure](https://portal.azure.com), ou pode consultar os valores usando PowerShell.

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>Verificar utilização

Pode utilizar o [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) cmdlet para verificar a utilização de quota.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

O resultado será semelhante ao seguinte:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Instâncias de VM Reservada
Instâncias de VM reservadas, que estão confinadas a uma subscrição individual sem flexibilidade de tamanho VM, adicionará um novo aspecto as quotas de vCPU. Estes valores descrevem o número de instâncias do tamanho declarado que tem de ser implementável na subscrição. Funcionam como um marcador de posição no sistema de quota para se certificar de que a quota é reservado para garantir que as instâncias VM reservadas são implementáveis na subscrição. Por exemplo, se uma subscrição específica tem 10 Standard_D1 o limite de utilizações para instâncias de máquina virtual reservada Standard_D1 reservados instâncias de VM será 10. Isso fará com que o Azure garantir que sempre há pelo menos 10 vCPUs disponíveis na quota de vCPUs regionais Total a ser utilizado para Standard_D1 instâncias e existem, pelo menos, 10 vCPUs disponíveis na quota de vCPU de família de D Standard a ser utilizado para instâncias de Standard_D1.

Se um aumento de quota é necessário comprar uma RI único de subscrição, pode [pedir um aumento de quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) na sua subscrição.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre faturação e de quotas, consulte [subscrição do Azure e limites do serviço, quotas e restrições](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
