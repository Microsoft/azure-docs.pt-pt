---
title: Configure a preferência de encaminhamento para um endereço IP público - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Saiba configurar a preferência de encaminhamento para um endereço IP público utilizando o Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 2a0a876b885d2fe59262cad660a3ebc5b5210fbb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598347"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Configure a preferência de encaminhamento para um endereço IP público utilizando o Azure PowerShell

Este artigo mostra-lhe como configurar a preferência de encaminhamento através da rede ISP (opção**Internet)** para um endereço IP público utilizando o Azure PowerShell. Depois de criar o endereço IP público, pode associá-lo aos seguintes recursos Azure para tráfego de entrada e saída para a internet:

* Máquina virtual
* Conjuntos de dimensionamento de máquinas virtuais
* Azure Kubernetes Service (AKS)
* Equilibrador de carga virado para a Internet
* Gateway de Aplicação
* Azure Firewall

Por padrão, a preferência de encaminhamento para o endereço IP público está definida para a rede global da Microsoft para todos os serviços Azure e pode ser associada a qualquer serviço Azure.

> [!IMPORTANT]
> A preferência por encaminhamento está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Se optar por instalar e utilizar o PowerShell localmente, este artigo requer a versão 6.9.0 do módulo PowerShell Azure. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade para a sua subscrição
A função De Preferência de Encaminhamento está atualmente em pré-visualização. Registe a funcionalidade para a sua subscrição da seguinte forma:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [o New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Criar um IP público com preferência de encaminhamento de Internet

O seguinte comando cria um novo IP público com um tipo de preferência de encaminhamento como *Internet* na região *de East US* Azure:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Pode associar o endereço IP público acima criado a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Utilize a secção CLI na página tutorial: [Associe um endereço IP público a uma máquina virtual](associate-public-ip-address-vm.md#azure-cli) para associar o IP público ao seu VM. Também pode associar o endereço IP público acima com um Balancer de [Carga Azure,](../load-balancer/load-balancer-overview.md)atribuindo-o à configuração **frontal** do equilíbrio de carga. O endereço IP público serve como endereço IP virtual (VIP) com balanceamento de carga.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não for necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a preferência de encaminhamento em endereços IP públicos](routing-preference-overview.md).
- [Configure a preferência de encaminhamento para um VM utilizando o Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
