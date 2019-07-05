---
title: Implantar o Firewall do Azure com vários endereços IP públicos com o Azure PowerShell
description: Neste artigo, saiba como implementar uma Firewall do Azure com vários endereços IP públicos com o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/2/2019
ms.author: victorh
ms.openlocfilehash: a5a53766df3338bb36913b589ebda970de55ec94
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491941"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implementar uma Firewall do Azure com vários endereços IP públicos com o Azure PowerShell

> [!IMPORTANT]
> Firewall do Azure com vários endereços IP públicos está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode implementar uma Firewall do Azure com até 100 endereços IP públicos.

Esta funcionalidade permite que os seguintes cenários:

- **DNAT** -é possível converter várias instâncias de porta padrão para os seus servidores de back-end. Por exemplo, se tiver dois endereços IP públicos, é possível converter a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT** -portas adicionais estão disponíveis para ligações SNAT de saída, reduzindo a possibilidade de exaustão de porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público de origem para utilizar uma ligação. Se tiver qualquer filtragem jusante na sua rede, terá de permitir que todos os endereços IP públicos associados com a firewall.

Os exemplos do Azure PowerShell seguintes mostram como pode adicionar, remover e configurar endereços IP públicos a Firewall do Azure.

> [!NOTE]
> Durante a pré-visualização pública, se adicionar ou remover um endereço IP público para um firewall em execução, conectividade de entrada existente usando regras DNAT pode não funcionar para 40-120 segundos. Não é possível remover o primeiro endereço IP público atribuído para o firewall, a menos que a firewall está desalocada ou eliminada.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicionar um endereço IP público a uma firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* como anexados à firewall.

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

Neste exemplo, o endereço IP público *azFwPublicIp1* como anular a exposição do firewall.

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

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
