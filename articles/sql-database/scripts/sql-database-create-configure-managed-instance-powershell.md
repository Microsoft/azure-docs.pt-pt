---
title: Exemplo do PowerShell - criar uma instância gerida na SQL Database do Azure | Documentos da Microsoft
description: Script de exemplo do PowerShell do Azure para criar uma instância gerida na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 929ab995ea76fa0d1d5227e3a53c2b50bc43fdc0
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729367"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Utilize o PowerShell para criar uma base de dados do SQL do Azure de instância gerida

Este exemplo de script do PowerShell cria uma instância gerida da base de dados do Azure SQL numa sub-rede dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Assim que o script foi executado com êxito, a instância gerida pode ser acedida em dentro da rede virtual ou a partir de um ambiente no local. Ver [configurar a VM do Azure para ligar a um Azure SQL Database Managed Instance](../sql-database-managed-instance-configure-vm.md) e [configurar uma ligação de ponto a site para uma instância de gerida de base de dados do Azure SQL no local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões suportadas](../sql-database-managed-instance-resource-limits.md#supported-regions) e [suportados tipos de subscrição](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o PowerShell de AZ 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.

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
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtém uma rede virtual num grupo de recursos |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define o estado de objetivos para uma rede virtual |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtém uma sub-rede numa rede virtual |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura o estado de objetivos para uma configuração de sub-rede numa rede virtual |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma tabela de rotas |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtém tabelas de rotas |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define o estado de objetivos para uma tabela de rotas |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria uma instância gerida da base de dados do Azure SQL |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).
