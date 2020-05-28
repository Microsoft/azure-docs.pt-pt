---
title: 'PowerShell: Criar uma instância'
titleSuffix: Azure SQL Managed Instance
description: Script de exemplo Azure PowerShell para criar uma instância gerida Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 9dfe6177ed46f133772a46930d1e56c6007b33f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053976"
---
# <a name="use-powershell-to-create-an-azure-sql-managed-instance"></a>Use powerShell para criar uma instância gerida Azure SQL
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script PowerShell cria um Caso Gerido Azure SQL numa subnet dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Uma vez que o script tenha sido executado com sucesso, o SQL Managed Instance pode ser acedido a partir de dentro da rede virtual ou de um ambiente no local. Consulte o [Configure Azure VM para ligar a uma instância gerida](../connect-vm-instance-configure.md) pela Base de Dados Azure SQL e [configurar uma ligação ponto-a-local a um Caso Gerido Azure SQL](../point-to-site-p2s-configure.md)a partir do local .

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](../resource-limits.md#supported-regions) e [tipos de subscrição suportados.](../resource-limits.md#supported-subscription-types)

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script usa alguns dos seguintes comandos. Para obter mais informações sobre os comandos usados e outros na tabela abaixo, clique nos links para comandar documentação específica.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados.
| [Rede Nova AzVirtual](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Adiciona uma configuração de sub-rede a uma rede virtual |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtém uma rede virtual num grupo de recursos |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define o estado objetivo para uma rede virtual |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtém uma sub-rede numa rede virtual |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura o estado-objetivo para uma configuração de sub-rede numa rede virtual |
| [Nova AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma tabela de rotas |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Obtém tabelas de rotas |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define o estado objetivo para uma mesa de rota |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria uma instância gerida azure SQL |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script powerShell de instância gerida sql podem ser encontradas nos [scripts PowerShell geridos pelo Azure SQL](../../database/powershell-script-content-guide.md).
