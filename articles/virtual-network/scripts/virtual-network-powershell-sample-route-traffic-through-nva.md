---
title: Exemplo de script do Azure PowerShell - encaminhar o tráfego através de uma aplicação virtual de rede | Microsoft Docs
description: Exemplo de script do Azure PowerShell - encaminhar o tráfego através de uma aplicação virtual de rede de firewall.
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
ms.openlocfilehash: 56b526b186acdd0903910a5a10f5fa3aa3c454df
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650080"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Encaminhar o tráfego através de um exemplo de script da aplicação virtual de rede

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. Também cria uma VM com o reencaminhamento de IP ativado para encaminhar o tráfego entre as duas sub-redes. Depois de executar o script pode implementar o software de rede, como uma aplicação de firewall, na VM.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/powershell) ou a partir de uma instalação local do PowerShell. Se utilizar o PowerShell localmente, este script requer o Az módulo do PowerShell versão 5.4.1 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crias as sub-redes de back-end e DMZ. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público para aceder à VM a partir da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria uma interface de rede virtual e ativa o reencaminhamento de IP para a mesma. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede (NSG). |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Associa os NSGs e as tabelas de rotas às sub-redes. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Cria uma tabela de rotas para todas as rotas. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Cria rotas para encaminhar o tráfego entre as sub-redes e a Internet através da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual e anexa o NIC à mesma. Este comando também especifica a imagem da máquina virtual a utilizar e as credenciais administrativas. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Elimina um grupo de recursos e todos os recursos contidos no mesmo. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell de rede virtual em [Exemplos do PowerShell de rede virtual](../powershell-samples.md).