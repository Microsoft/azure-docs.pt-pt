---
title: Implantar o Firewall do Azure com zonas de disponibilidade com o Azure PowerShell
description: Neste artigo, saiba como implementar uma Firewall do Azure com zonas de disponibilidade com o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704005"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Implementar uma Firewall do Azure com zonas de disponibilidade com o Azure PowerShell

Firewall do Azure pode ser configurado durante a implementação para abranger várias zonas de disponibilidade para maior disponibilidade.

Esta funcionalidade permite que os seguintes cenários:

- Pode aumentar a disponibilidade de 99,99% de tempo de atividade. Para obter mais informações, consulte o Firewall do Azure [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O tempo de atividade de 99,99% são oferecidos SLAS quando duas ou mais zonas de disponibilidade estão selecionadas.
- Também pode associar Firewall do Azure para uma zona específica apenas por motivos de proximidade, usando o SLA de 99,95% standard do serviço.

Para obter mais informações sobre as zonas de disponibilidade de Firewall do Azure, consulte [o que é o Firewall do Azure?](overview.md)

O exemplo do Azure PowerShell seguinte mostra como pode implementar uma Firewall do Azure com zonas de disponibilidade.

## <a name="create-a-firewall-with-availability-zones"></a>Criar uma firewall com zonas de disponibilidade

Este exemplo cria uma firewall em zonas 1, 2 e 3.

Quando é criado o endereço IP público standard, não é especificada nenhuma zona específica. Esta ação cria um endereço IP com redundância de zona por predefinição. Endereços IP públicos Standard podem ser configurados no todas as zonas ou numa única zona.

É importante saber, porque não pode ter um firewall na zona 1 e um endereço IP na zona 2. Mas pode ter um firewall na zona 1 e o endereço IP em todas as zonas, ou uma firewall e um endereço IP na mesma zona única para fins de proximidade.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
