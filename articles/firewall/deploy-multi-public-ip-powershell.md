---
title: Implantar o Firewall do Azure com vários endereços IP públicos com o Azure PowerShell
description: Neste artigo, saiba como implementar uma Firewall do Azure com vários endereços IP públicos com o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703991"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implementar uma Firewall do Azure com vários endereços IP públicos com o Azure PowerShell

> [!IMPORTANT]
> Firewall do Azure com vários endereços IP públicos está disponível através do Azure PowerShell, CLI do Azure, REST e modelos. A interface de utilizador do portal está a ser adicionada para as regiões de forma incremental e vai estar disponível em todas as regiões quando a implementação estiver concluída.

Pode implementar uma Firewall do Azure com até 100 endereços IP públicos.

Esta funcionalidade permite que os seguintes cenários:

- **DNAT** -é possível converter várias instâncias de porta padrão para os seus servidores de back-end. Por exemplo, se tiver dois endereços IP públicos, é possível converter a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT** -portas adicionais estão disponíveis para ligações SNAT de saída, reduzindo a possibilidade de exaustão de porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público de origem para utilizar uma ligação. Se tiver qualquer filtragem jusante na sua rede, terá de permitir que todos os endereços IP públicos associados com a firewall.

Os exemplos do Azure PowerShell seguintes mostram como pode configurar, adicionar e remover endereços IP públicos a Firewall do Azure.

> [!NOTE]
> Não é possível remover o ipConfiguration primeiro a partir da página Configuração do Firewall do Azure pública IP endereço. Se pretender modificar o endereço IP, pode utilizar o Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Criar uma firewall com duas ou mais endereços IP públicos

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicionar um endereço IP público a uma firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* está anexado à firewall.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Remover um endereço IP público de uma firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é desligado do firewall.

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

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
