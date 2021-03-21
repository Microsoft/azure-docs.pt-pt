---
title: Implementar firewall Azure com zonas de disponibilidade usando PowerShell
description: Neste artigo, aprende-se a implementar uma Firewall Azure com Zonas de Disponibilidade utilizando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94656110"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Implementar uma Firewall do Azure com Zonas de Disponibilidade através do Azure PowerShell

O Azure Firewall pode ser configurado durante a implementação para abranger várias Zonas de Disponibilidade para uma maior disponibilidade.

Esta funcionalidade permite os seguintes cenários:

- Pode aumentar a disponibilidade para 99,99% de tempo de desconto. Para obter mais informações, consulte o Acordo de [Nível de Serviço de Firewall Azure Firewall (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O SLA de 99,99% é oferecido quando são selecionadas duas ou mais Zonas de Disponibilidade.
- Também pode associar o Azure Firewall a uma zona específica apenas por razões de proximidade, utilizando o serviço padrão 99.95% SLA.

Para obter mais informações sobre as Zonas de Disponibilidade de Firewall Azure, veja [o que é Azure Firewall?](overview.md)

O exemplo Azure PowerShell mostra como pode implementar uma Firewall Azure com Zonas de Disponibilidade.

## <a name="create-a-firewall-with-availability-zones"></a>Criar uma firewall com Zonas de Disponibilidade

Este exemplo cria uma firewall nas zonas 1, 2 e 3.

Quando o endereço IP público padrão é criado, nenhuma zona específica é especificada. Isto cria um endereço IP redundante por padrão. Os endereços IP públicos padrão podem ser configurados em todas as zonas, ou numa única zona.

É importante saber, porque não se pode ter uma firewall na zona 1 e um endereço IP na zona 2. Mas você pode ter uma firewall na zona 1 e endereço IP em todas as zonas, ou uma firewall e um endereço IP na mesma zona única para fins de proximidade.

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
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)