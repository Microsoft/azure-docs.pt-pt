---
title: Exemplo do PowerShell – criar uma instância gerenciada no banco de dados SQL do Azure
description: Azure PowerShell script de exemplo para criar uma instância gerenciada no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 3e72a2f6754ad8e9c5bcfabe7eeee299468fa8f3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691635"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Usar o PowerShell para criar uma instância gerenciada do banco de dados SQL do Azure

Este exemplo de script do PowerShell cria uma instância gerenciada do banco de dados SQL do Azure em uma sub-rede dedicada em uma nova rede virtual. Ele também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Depois que o script tiver sido executado com êxito, a instância gerenciada poderá ser acessada de dentro da rede virtual ou de um ambiente local. Consulte [Configurar a VM do Azure para se conectar a um instância gerenciada do banco de dados SQL do Azure](../sql-database-managed-instance-configure-vm.md) e [Configurar uma conexão ponto a site com um instância gerenciada do banco de dados SQL do Azure local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para obter limitações, consulte [regiões com suporte](../sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de assinatura com suporte](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Adiciona uma configuração de sub-rede a uma rede virtual |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obter uma rede virtual em um grupo de recursos |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define o estado da meta para uma rede virtual |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obter uma sub-rede em uma rede virtual |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura o estado de meta para uma configuração de sub-rede em uma rede virtual |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma tabela de rotas |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtém tabelas de rotas |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define o estado da meta para uma tabela de rotas |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria uma instância gerenciada do banco de dados SQL do Azure |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
