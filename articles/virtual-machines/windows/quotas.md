---
title: vCPU quotas para Azure
description: Saiba mais sobre as quotas vCPU para máquinas virtuais Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: b00d84e1c2dc8f104046fe24360d79e6952dfacc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950294"
---
# <a name="check-vcpu-quotas-using-azure-powershell"></a>Verifique as quotas vCPU utilizando a Azure PowerShell

As quotas vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais estão dispostas em dois níveis para cada subscrição, em cada região. O primeiro nível é o Total Regional vCPUs, e o segundo nível são os vários núcleos familiares de tamanho VM, como os vCPUs da série D. Sempre que um novo VM é implantado, os vCPUs para o VM não devem exceder a quota vCPU para a família do tamanho VM ou a quota total de vCPU regional. Se uma dessas quotas for excedida, a implantação em VM não será permitida. Existe também uma quota para o número global de máquinas virtuais na região. Os detalhes sobre cada uma destas quotas podem ser vistos na secção **Utilização + quotas** da página **Subscrição** no [portal Azure](https://portal.azure.com), ou pode consultar os valores que utilizam o PowerShell.

> [!NOTE]
> A quota é calculada com base no número total de núcleos utilizados, tanto atribuídos como com deallocados. Se precisar de núcleos adicionais, [solicite um aumento de quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) ou elimine VMs que já não são necessários. 
 
## <a name="check-usage"></a>Verificar utilização

Pode utilizar o [cmdlet Get-AzVMUsage](/powershell/module/az.compute/get-azvmusage) para verificar a utilização da sua quota.

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
As instâncias VM reservadas, que são procuradas numa única subscrição sem flexibilidade do tamanho de VM, adicionarão um novo aspeto às quotas vCPU. Estes valores descrevem o número de casos do tamanho indicado que devem ser implantáveis na subscrição. Trabalham como espaço reservado no sistema de quotas para garantir que a quota é reservada para garantir que as instâncias VM reservadas sejam implantados na subscrição. Por exemplo, se uma subscrição específica tiver 10 Standard_D1 de VM reservados, o limite de utilização para Standard_D1 casos de VM reservados será de 10. Isto fará com que a Azure garanta que existem sempre pelo menos 10 vCPUs disponíveis na quota total regional de vCPUs para Standard_D1 casos e existem pelo menos 10 vCPUs disponíveis no contingente vCPU da família D a utilizar para Standard_D1 casos.

Se for necessário um aumento de quota para adquirir um RI de assinatura única, pode [solicitar um aumento de quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) na sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre faturação e quotas, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)
