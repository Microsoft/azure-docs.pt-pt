---
title: Exemplos de scripts do Azure PowerShell para a Base de Dados SQL | Microsoft Docs
description: Exemplos de scripts do Azure PowerShell para ajudar a criar e gerir servidores de Base de Dados SQL do Azure, conjuntos elásticos, bases de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3a22caa39dad3ce296c43015f324270b7dc55ae8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862034"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemplos do Azure PowerShell para a Base de Dados SQL do Azure

Base de dados SQL do Azure permite-lhe configurar seus bancos de dados, instâncias e conjuntos de com o Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o PowerShell de AZ 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="single-database-and-elastic-pools"></a>Conjuntos de base de dados e elástico únicos

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar bases de dados individuais e conjuntos elásticos**||
| [Criar uma base de dados e configurar uma regra de firewall do servidor de base de dados](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados agrupadas](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria conjuntos elásticos da Base de Dados SQL do Azure, move bases de dados agrupadas e altera os tamanhos de computação.|
|**Configurar georreplicação e ativação pós-falha**||
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados agrupada através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure num conjunto elástico SQL e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de um grupo de ativação pós-falha para uma base de dados](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura um grupo de ativação pós-falha para uma instância de servidor da Base de Dados SQL do Azure, adiciona uma base de dados ao grupo de ativação pós-falha e efetua a ativação pós-falha para o servidor secundário |
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de uma base de dados SQL do Azure, dimensiona-a para um tamanho de computação superior e cria uma regra de alerta numa das métricas de desempenho. |
| [Dimensionar um conjunto elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico da Base de Dados SQL do Azure, dimensiona-o para um tamanho de computação superior e cria uma regra de alerta numa das métricas de desempenho.  |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura as políticas de auditoria e deteção de ameaças para uma base de dados SQL do Azure. |
| **Restaurar, copiar e importar uma base de dados**||
| [Restaurar uma base de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura uma base de dados SQL do Azure a partir de uma cópia de segurança georredundante e restaura uma base de dados SQL do Azure eliminada para a cópia de segurança mais recente. |
| [Copiar uma base de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell cria uma cópia de uma base de dados SQL do Azure existente num novo servidor SQL do Azure. |
| [Importar uma base de dados de um ficheiro bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell importa uma base de dados para um servidor SQL do Azure a partir de um ficheiro bacpac. |
| **Sincronizar dados entre bases de dados**||
| [Sincronizar dados entre bases de dados SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados entre várias bases de dados SQL do Azure. |
| [Sincronizar dados entre a Base de Dados SQL e o SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados entre uma base de dados SQL do Azure e uma base de dados do SQL Server no local. |
| [Atualizar o esquema de Sincronização de Dados SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell adiciona ou remove itens do esquema de Sincronização de Dados. |
|||

Saiba mais sobre o [única base de dados do Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instância Gerida

A tabela seguinte inclui ligações para scripts do PowerShell do Azure de exemplo para o Azure SQL Database - instância gerida.

| |  |
|---|---|
|**Criar e configurar instâncias geridas**||
| [Criar e gerir uma Instância Gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Este script do PowerShell mostra como criar e gerir uma Instância Gerida com o Azure PowerShell |
| [Criar e gerir uma instância gerida com o modelo Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell mostra-lhe como criar e gerir uma instância gerida com o modelo do Azure PowerShell e do Azure Resource Manager.|
| **Configurar a encriptação de dados transparente (TDE)**||
| [Gerir a encriptação de dados transparente numa instância gerida com a sua própria chave de Cofre de chaves do Azure](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura encriptação de dados transparente (TDE) o cenário traga a sua própria chave para SQL instância gerida do Azure, com uma chave do Azure Key Vault|
|||

Saiba mais sobre o [API de PowerShell do Azure de instância gerida](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).
