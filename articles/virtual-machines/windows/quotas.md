---
title: quotas vCPU para Azure
description: Conheça as quotas vCPU para o Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893469"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As quotas vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais estão dispostas em dois níveis para cada subscrição, em cada região. O primeiro nível é o Total Regional vCPUs, e o segundo nível são os vários núcleos familiares de tamanho VM, como os vCPUs da série D. Sempre que um novo VM for implantado, as vCPUs para o VM não devem exceder a quota vCPU para a família VM ou a quota total regional de VCPU. Se uma dessas quotas for excedida, a implantação da VM não será permitida. Existe também uma quota para o número global de máquinas virtuais na região. Os detalhes de cada uma destas quotas podem ser vistos na secção **Utilização + quotas** da página **de Subscrição** no [portal Azure,](https://portal.azure.com)ou pode consultar os valores que utilizam o PowerShell.

   
 
## <a name="check-usage"></a>Verificar utilização

Pode utilizar o cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) para verificar a utilização da sua quota.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

A saída será semelhante a esta:

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
As Instâncias VM reservadas, que são dimensionadas para uma única subscrição sem flexibilidade de tamanho VM, adicionarão um novo aspeto às quotas vCPU. Estes valores descrevem o número de instâncias da dimensão indicada que devem ser implantáveis na subscrição. Funcionam como espaço reservados no sistema de quotas para garantir que a quota é reservada para garantir que as instâncias VM reservadas sejam implantáveis na subscrição. Por exemplo, se uma subscrição específica tiver 10 Standard_D1 vM reservado, o limite de utilizações para Standard_D1 casos de VM reservados será de 10. Isto fará com que o Azure garanta que existem sempre pelo menos 10 vCPUs disponíveis na quota total regional de vCPUs a utilizar em Standard_D1 casos e existem pelo menos 10 vCPUs disponíveis na quota VCPU família Standard D para serem utilizadas em Standard_D1 casos.

Se for necessário um aumento de quota para adquirir um RI de subscrição única, pode [solicitar um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota na sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre faturação e quotas, consulte os limites de subscrição e serviço do [Azure, quotas e constrangimentos.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)
