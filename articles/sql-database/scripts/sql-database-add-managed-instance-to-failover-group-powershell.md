---
title: Exemplo do PowerShell – grupo de failover – instância gerenciada do banco de dados SQL do Azure
description: Azure PowerShell script de exemplo para criar uma instância gerenciada do banco de dados SQL do Azure, adicioná-la a um grupo de failover e failover de teste.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691755"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Usar o PowerShell para adicionar uma instância gerenciada do banco de dados SQL do Azure a um grupo de failover 

Este exemplo de script do PowerShell cria duas instâncias gerenciadas, adiciona-as a um grupo de failover e, em seguida, testa o failover da instância gerenciada primária para a instância gerenciada secundária. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele. Você precisará remover o grupo de recursos duas vezes. Remover o grupo de recursos na primeira vez removerá a instância gerenciada e os clusters virtuais, mas irá falhar com a mensagem de erro `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Execute o comando Remove-AzResourceGroup uma segunda vez para remover todos os recursos residuais, bem como o grupo de recursos.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos do Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adiciona uma configuração de sub-rede a uma rede virtual. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém uma rede virtual num grupo de recursos. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém uma sub-rede em uma rede virtual. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Cria uma tabela de rotas. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Atualiza uma configuração de sub-rede para uma rede virtual.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Atualiza uma rede virtual.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Obtém um grupo de segurança de rede. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Atualiza um grupo de segurança de rede.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Adiciona uma rota a uma tabela de rotas. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Atualiza uma tabela de rotas.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Cria uma instância gerenciada do banco de dados SQL do Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Retorna informações sobre a instância do banco de dados gerenciado do Azure SQL. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma configuração de IP para um gateway de rede virtual |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um gateway de rede virtual |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Cria uma conexão entre os dois gateways de rede virtual.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Cria um novo grupo de failover de instância gerenciada do banco de dados SQL do Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Obtém ou lista os grupos de failover de instância gerenciada.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Executa um failover de um grupo de failover de instância gerenciada. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos. | 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
