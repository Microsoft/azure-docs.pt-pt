---
title: Implemente firewall azure com vários endereços IP públicos usando powerShell
description: Neste artigo, aprende-se a implementar um Firewall Azure com vários endereços IP públicos utilizando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: 178daba47acaf8ad161a0634c16b046ec330abb6
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864432"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implementar uma Firewall do Azure com vários endereços IP públicos através do Azure PowerShell

Esta funcionalidade permite os seguintes cenários:

- **DNAT** - Pode traduzir várias instâncias de porta padrão para os seus servidores de backend. Por exemplo, se tiver dois endereços IP públicos, pode traduzir a porta TCP 3389 (RDP) para ambos os endereços IP.
- **SNAT** - Portos adicionais estão disponíveis para ligações SNAT de saída, reduzindo o potencial de exaustão da porta SNAT. Neste momento, o Azure Firewall seleciona aleatoriamente o endereço IP público de origem para utilizar para uma ligação. Se tiver alguma filtragem a jusante na sua rede, tem de permitir todos os endereços IP públicos associados à sua firewall. Considere utilizar um [prefixo de endereço IP público](../virtual-network/public-ip-address-prefix.md) para simplificar esta configuração.
 
O Azure Firewall com vários endereços IP públicos está disponível através do portal Azure, Azure PowerShell, Azure CLI, REST e modelos. Pode implementar um Azure Firewall com até 250 endereços IP públicos.

Os exemplos do Azure PowerShell mostram como pode configurar, adicionar e remover endereços IP públicos para firewall azure.

> [!NOTE]
> Não é possível remover a primeira configuração ipConfiguration da página de configuração de endereçoIP público da Firewall. Se pretender modificar o endereço IP, pode utilizar o Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Criar uma firewall com dois ou mais endereços IP públicos

Este exemplo cria uma firewall anexada à rede virtual *vnet* com dois endereços IP públicos.

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicione um endereço IP público a uma firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* está ligado à firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Remova um endereço IP público de uma firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é desligado da firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar uma Firewall Azure com vários endereços IP públicos - modelo de Gestor de Recursos](quick-create-multiple-ip-template.md)
