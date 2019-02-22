---
title: Exemplo de script do Azure PowerShell - Filtrar o tráfego de rede de VM | Microsoft Docs
description: Exemplo de script do Azure PowerShell - Filtrar o tráfego de rede de VM de entrada e saída.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 9be13fb12d23457031bdb40ba5234de5b35ce503
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651928"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Exemplo de script de filtragem de tráfego de rede VM de entrada e saída

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP e HTTPS, enquanto o tráfego de saída à Internet da sub-rede de back-end não é permitido. Depois de executar o script, tem uma máquina virtual com dois NICs. Cada NIC está ligado a outra sub-rede.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/powershell) ou a partir de uma instalação local do PowerShell. Se utilizar o PowerShell localmente, este script requer o Azure PowerShell versão 1.0.0 do módulo ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria um objeto de configuração de sub-rede |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria regras de segurança a serem atribuídas a um grupo de segurança de rede. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Associa os NSGs a sub-redes. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público para aceder à VM a partir da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria interfaces de rede virtual e anexa-as a sub-redes de front-end e back-end da rede virtual. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell de rede virtual em [Exemplos do PowerShell de rede virtual](../powershell-samples.md).
