---
title: cotas do vCPU para o Azure | Microsoft Docs
description: Saiba mais sobre as cotas do vCPU para o Azure.
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
ms.openlocfilehash: 48da419cdf5b4555e06b2a87d4b6821276777826
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102471"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As cotas de vCPU para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é o total regional de vCPUs e a segunda camada são os vários núcleos de família de tamanho de VM, como o vCPUs da série D. Sempre que uma nova VM for implantada, o vCPUs da VM não deverá exceder a cota de vCPU para a família de tamanho da VM ou a cota de vCPU regional total. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos na seção **uso + cotas** da página **assinatura** no [portal do Azure](https://portal.azure.com)ou você pode consultar os valores usando o PowerShell.

 [!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)] 
 
## <a name="check-usage"></a>Verificar utilização

Você pode usar o cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) para verificar o uso de sua cota.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

A saída terá uma aparência semelhante a esta:

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


## <a name="reserved-vm-instances"></a>Instâncias Reservadas da VM
As instâncias de VM reservadas, que têm o escopo de uma única assinatura sem flexibilidade de tamanho de VM, adicionarão um novo aspecto às cotas de vCPU. Esses valores descrevem o número de instâncias do tamanho declarado que devem ser implantáveis na assinatura. Eles funcionam como um espaço reservado no sistema de cotas para garantir que a cota seja reservada para garantir que as instâncias de VM reservadas sejam implantáveis na assinatura. Por exemplo, se uma assinatura específica tiver 10 instâncias de VM reservadas Standard_D1, o limite de usos para as instâncias de VM reservadas Standard_D1 será 10. Isso fará com que o Azure Verifique se há sempre pelo menos 10 vCPUs disponíveis na cota de vCPUs regional total a ser usada para instâncias de Standard_D1 e se há pelo menos 10 vCPUs disponíveis na cota de vCPU da família D padrão a ser usada para instâncias de Standard_D1.

Se um aumento de cota for necessário para comprar uma única assinatura RI, você poderá [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) em sua assinatura.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre cobrança e cotas, consulte [assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
