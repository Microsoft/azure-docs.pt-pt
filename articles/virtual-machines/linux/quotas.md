---
title: cotas do vCPU para o Azure
description: Saiba mais sobre as cotas do vCPU para o Azure.
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
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 04ab96058650e6165a745b48f4c0fc213c2f9412
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035175"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quotas de vCPU de máquina virtual

As cotas de vCPU para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais são organizadas em duas camadas para cada assinatura, em cada região. A primeira camada é o total regional de vCPUs e a segunda camada são os vários núcleos de família de tamanho de VM, como o vCPUs da série D. Sempre que uma nova VM for implantada, o vCPUs da VM não deverá exceder a cota de vCPU para a família de tamanho da VM ou a cota de vCPU regional total. Se qualquer uma dessas cotas for excedida, a implantação da VM não será permitida. Também há uma cota para o número total de máquinas virtuais na região. Os detalhes sobre cada uma dessas cotas podem ser vistos na seção **uso + cotas** da página **assinatura** no [portal do Azure](https://portal.azure.com)ou você pode consultar os valores usando o CLI do Azure.


## <a name="check-usage"></a>Verificar utilização

Você pode verificar seu uso de cota usando [AZ VM List-Usage](/cli/azure/vm).

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
As instâncias de VM reservadas, que têm o escopo de uma única assinatura sem flexibilidade de tamanho de VM, adicionarão um novo aspecto às cotas de vCPU. Esses valores descrevem o número de instâncias do tamanho declarado que devem ser implantáveis na assinatura. Eles funcionam como um espaço reservado no sistema de cotas para garantir que a cota seja reservada para garantir que as reservas do Azure sejam implantáveis na assinatura. Por exemplo, se uma assinatura específica tiver 10 Standard_D1 reservas, o limite de usos para as reservas de Standard_D1 será 10. Isso fará com que o Azure Verifique se há sempre pelo menos 10 vCPUs disponíveis na cota de vCPUs regional total a ser usada para Standard_D1 instâncias e se há pelo menos 10 vCPUs disponíveis na cota de vCPU da família D padrão para serem usadas para Standard_D1 instâncias.

Se um aumento de cota for necessário para comprar uma única assinatura RI, você poderá [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) em sua assinatura.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre cobrança e cotas, consulte [assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
