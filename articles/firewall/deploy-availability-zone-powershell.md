---
title: Implementar firewall azure com zonas de disponibilidade usando powerShell
description: Neste artigo, aprende a implementar uma Firewall Azure com Zonas de Disponibilidade utilizando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195911"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Implementar uma Firewall do Azure com Zonas de Disponibilidade através do Azure PowerShell

O Firewall Azure pode ser configurado durante a implementação para abranger várias Zonas de Disponibilidade para maior disponibilidade.

Esta funcionalidade permite os seguintes cenários:

- Pode aumentar a disponibilidade para 99,99% de tempo de uptime. Para mais informações, consulte o Acordo de Nível de Serviço de Firewall Azure [(SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). O SLA de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade são selecionadas.
- Também pode associar o Azure Firewall a uma zona específica apenas por razões de proximidade, utilizando o padrão de serviço 99.95% SLA.

Para mais informações sobre as Zonas de Disponibilidade de Firewall Azure, consulte [o que é o Azure Firewall?](overview.md)

O exemplo do Azure PowerShell mostra como pode implantar uma Firewall Azure com Zonas de Disponibilidade.

## <a name="create-a-firewall-with-availability-zones"></a>Criar uma firewall com Zonas de Disponibilidade

Este exemplo cria uma firewall nas zonas 1, 2 e 3.

Quando o endereço IP público padrão é criado, nenhuma zona específica é especificada. Isto cria um endereço IP redundante por defeito. Os endereços IP públicos padrão podem ser configurados em todas as zonas, ou numa única zona.

É importante saber, porque não se pode ter uma firewall na zona 1 e um endereço IP na zona 2. Mas pode ter uma firewall no endereço 1 e IP da zona 1 e IP em todas as zonas, ou uma firewall e um endereço IP na mesma zona única para fins de proximidade.

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

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: monitorizar registos do Azure Firewall](./tutorial-diagnostics.md)
