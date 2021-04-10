---
title: Quotas de vCPU
description: Saiba mais sobre as quotas vCPU para Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 1b9c0d50754d582ca7ada5d0b46c6f998b59d3ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549571"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Verifique as quotas vCPU utilizando o Azure CLI

As quotas vCPU para máquinas virtuais e conjuntos de escala de máquinas virtuais estão dispostas em dois níveis para cada subscrição, em cada região. O primeiro nível é o Total Regional vCPUs, e o segundo nível são os vários núcleos familiares de tamanho VM, como os vCPUs da série D. Sempre que um novo VM é implantado, os vCPUs para o VM não devem exceder a quota vCPU para a família do tamanho VM ou a quota total de vCPU regional. Se uma dessas quotas for excedida, a implantação em VM não será permitida. Existe também uma quota para o número global de máquinas virtuais na região. Os detalhes sobre cada uma destas quotas podem ser vistos na secção **Utilização + quotas** da página **Subscrição** no [portal Azure,](https://portal.azure.com)ou pode consultar os valores usando o CLI Azure.

> [!NOTE]
> A quota é calculada com base no número total de núcleos utilizados, tanto atribuídos como com deallocados. Se precisar de núcleos adicionais, [solicite um aumento de quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) ou elimine VMs que já não são necessários. 


## <a name="check-usage"></a>Verificar utilização

Pode verificar a utilização da sua quota [utilizando a utilização da lista az vm](/cli/azure/vm).

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
As instâncias VM reservadas, que são procuradas numa única subscrição sem flexibilidade do tamanho de VM, adicionarão um novo aspeto às quotas vCPU. Estes valores descrevem o número de casos do tamanho indicado que devem ser implantáveis na subscrição. Trabalham como espaço reservado no sistema de quotas para garantir que a quota é reservada para garantir que as reservas do Azure sejam implantas na subscrição. Por exemplo, se uma subscrição específica tiver 10 Standard_D1 reservas, o limite de utilização para Standard_D1 reservas será de 10. Isto fará com que a Azure garanta que existem sempre pelo menos 10 vCPUs disponíveis na quota total regional de vCPUs para Standard_D1 casos e existem pelo menos 10 vCPUs disponíveis no contingente vCPU da família D a utilizar para Standard_D1 casos.

Se for necessário um aumento de quota para adquirir um RI de assinatura única, pode [solicitar um aumento de quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) na sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre faturação e quotas, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)
