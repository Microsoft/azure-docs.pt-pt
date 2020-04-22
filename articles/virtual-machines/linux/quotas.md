---
title: Quotas de vCPU
description: Conheça as quotas vCPU para o Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 4f8d181194328725c2e7c02f6e73752cf568ef4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759182"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As quotas vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais estão dispostas em dois níveis para cada subscrição, em cada região. O primeiro nível é o Total Regional vCPUs, e o segundo nível são os vários núcleos familiares de tamanho VM, como os vCPUs da série D. Sempre que um novo VM for implantado, as vCPUs para o VM não devem exceder a quota vCPU para a família VM ou a quota total regional de VCPU. Se uma dessas quotas for excedida, a implantação da VM não será permitida. Existe também uma quota para o número global de máquinas virtuais na região. Os detalhes de cada uma destas quotas podem ser vistos na secção **Utilização + quotas** da página **de Subscrição** no [portal Azure,](https://portal.azure.com)ou pode consultar os valores que utilizam o Azure CLI.


## <a name="check-usage"></a>Verificar utilização

Pode verificar o uso da sua quota utilizando a [utilização da lista Az vm](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

O resultado deverá ter um aspeto semelhante ao seguinte:


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
As Instâncias VM reservadas, que são dimensionadas para uma única subscrição sem flexibilidade de tamanho VM, adicionarão um novo aspeto às quotas vCPU. Estes valores descrevem o número de instâncias da dimensão indicada que devem ser implantáveis na subscrição. Funcionam como espaço reservadono no sistema de quotas para garantir que a quota é reservada para garantir que as reservas do Azure sejam implantáveis na subscrição. Por exemplo, se uma subscrição específica tiver 10 Standard_D1 reserva o limite de utilizações para Standard_D1 reservas será de 10. Isto fará com que o Azure garanta que existem sempre pelo menos 10 vCPUs disponíveis na quota total regional de vCPUs a utilizar em Standard_D1 casos e existem pelo menos 10 vCPUs disponíveis na quota VCPU família Standard D para serem utilizadas em Standard_D1 casos.

Se for necessário um aumento de quota para comprar uma Única Subscrição RI, pode [solicitar um aumento](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) de quota na sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre faturação e quotas, consulte os limites de subscrição e serviço do [Azure, quotas e constrangimentos.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json)
