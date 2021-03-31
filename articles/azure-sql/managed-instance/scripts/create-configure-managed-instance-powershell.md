---
title: 'PowerShell: Criar um caso gerido'
titleSuffix: Azure SQL Managed Instance
description: Este artigo fornece um script exemplo Azure PowerShell para criar um exemplo gerido.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323836"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Use o PowerShell para criar um caso gerido

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script PowerShell cria uma instância gerida numa sub-rede dedicada dentro de uma nova rede virtual. Também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Uma vez que o script tenha sido executado com sucesso, a instância gerida pode ser acedida a partir da rede virtual ou de um ambiente no local. Consulte [o Configure Azure VM para ligar à Azure SQL Database Managed Instance](../connect-vm-instance-configure.md) e [Configure uma ligação ponto-a-local à Azure SQL Managed Instance a partir do local](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões apoiadas](../resource-limits.md#supported-regions) e [tipos de assinaturas suportados.](../resource-limits.md#supported-subscription-types)

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a Azure PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Utilize o seguinte comando para remover o grupo de recursos e todos os recursos que lhe estão associados.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza alguns dos seguintes comandos. Para obter mais informações sobre os comandos usados e outros na tabela abaixo, clique nos links para comandar documentação específica.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Adiciona uma configuração de sub-rede a uma rede virtual. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Obtém uma rede virtual num grupo de recursos. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Define o estado de meta para uma rede virtual. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Obtém uma sub-rede numa rede virtual. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura o estado de meta para uma configuração de sub-rede numa rede virtual. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Cria uma mesa de rotas. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Tem mesas de rota. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Define o estado de meta para uma tabela de rotas. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Cria um caso gerido. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a Azure PowerShell, consulte [a documentação da Azure PowerShell](/powershell/azure/).

As amostras adicionais de script powerShell para Azure SQL Managed Instance podem ser encontradas em [scripts powerShell de instância gerida Azure SQL](../../database/powershell-script-content-guide.md).
