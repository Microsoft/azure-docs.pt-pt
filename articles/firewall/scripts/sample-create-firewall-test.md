---
title: Criar um ambiente de teste do Azure Firewall
description: Este exemplo de script cria uma firewall e um ambiente de rede de teste. A rede tem um VNet, com três sub-redes.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658371"
---
# <a name="create-an-azure-firewall-test-environment"></a>Criar um ambiente de teste do Azure Firewall

Este exemplo de script cria uma firewall e um ambiente de rede de teste. A rede tem uma VNet, com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet* e *JumpboxSubnet*. ServersSubnet e JumpboxSubnet têm um Windows Server de 2 núcleos em cada um.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A firewall encontra-se na AzureFirewallSubnet e está configurada com uma Coleção de Regras de Aplicação com uma única regra que permite o acesso a `www.microsoft.com` .

É criada uma rota definida pelo utilizador que aponta o tráfego de rede de ServersSubnet através da firewall, em que são aplicadas as regras de firewall.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/powershell) ou a partir de uma instalação local do PowerShell. 

Se executar o PowerShell localmente, este script requer Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. 

Pode utilizar o `PowerShellGet` se precisar de atualizar, o qual está incorporado no Windows 10 e no Windows Server 2016.

> [!NOTE]
>Outra versão do Windows requer a instalação do `PowerShellGet` antes de poder utilizá-lo. Pode executar `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` para determinar se está instalado no seu sistema. Se a saída estiver em branco, tem de instalar a versão mais recente do [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Para mais informações, consulte [instalar a Azure PowerShell](/powershell/azure/install-Az-ps)

Qualquer instalação existente do Azure PowerShell feita com o instalador de Plataforma Web entrará em conflito com a instalação do PowerShellGet e terá de ser removida.

Não se esqueça de que, se estiver a executar o PowerShell localmente, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria um objeto de configuração de sub-rede |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria regras de segurança a serem atribuídas a um grupo de segurança de rede. |
| [Grupo deSegurança New-AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Associa os NSGs a sub-redes. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público para aceder à VM a partir da Internet. |
| [Novo AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria interfaces de rede virtual e anexa-as a sub-redes de front-end e back-end da rede virtual. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| Cria um Azure Firewall novo.|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Obtém um objeto do Azure Firewall.|
|[Nova-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|Cria uma regra de aplicação do Azure Firewall nova.|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Consolida alterações ao objeto do Azure Firewall.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).