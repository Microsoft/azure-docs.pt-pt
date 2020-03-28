---
title: Exemplos de scripts Azure PowerShell
description: Exemplos de scripts do Azure PowerShell para ajudar a criar e gerir servidores da Base de Dados SQL do Azure, conjuntos elásticos, bases de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73821404"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemplos do Azure PowerShell para a Base de Dados SQL do Azure

A Base de Dados Azure SQL permite-lhe configurar as suas bases de dados, instâncias e piscinas utilizando o Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="single-database-and-elastic-pools"></a>[Base de Dados Única e Piscinas Elásticas](#tab/single-database)

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar bases de dados individuais e piscinas elásticas**||
| [Criar uma única base de dados e configurar uma regra de firewall do servidor de base de dados](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria conjuntos elásticos da Base de Dados SQL do Azure, move bases de dados agrupadas e altera os tamanhos da computação.|
|**Configurar georreplicação e ativação pós-falha**||
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure num conjunto elástico SQL e realiza a ativação pós-falha para uma réplica secundária. |
|**Configure um grupo de failover**||
| [Configure um grupo de failover para uma única base de dados](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria uma base de dados e um grupo failover, adiciona a base de dados ao grupo failover e testa falha no servidor secundário. | 
| [Configure um grupo de failover para uma piscina elástica](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria uma base de dados, adiciona-a a uma piscina elástica, adiciona a piscina elástica ao grupo failover e testa falha no servidor secundário. | 
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de uma base de dados SQL do Azure, dimensiona-a para um tamanho da computação superior e cria uma regra de alerta numa das métricas de desempenho. |
| [Dimensionar um conjunto elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico da Base de Dados SQL do Azure, dimensiona-o para um tamanho da computação superior e cria uma regra de alerta numa das métricas de desempenho. |
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

Saiba mais sobre a [Base de Dados Única Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>[Instância Gerida](#tab/managed-instance)

A tabela seguinte inclui links para a amostra de scripts Azure PowerShell para base de dados Azure SQL - Instância Gerida.

| |  |
|---|---|
|**Criar e configurar casos geridos**||
| [Criar e gerir uma Instância Gerida](scripts/sql-database-create-configure-managed-instance-powershell.md) | Este script do PowerShell mostra como criar e gerir uma Instância Gerida com o Azure PowerShell |
| [Criar e gerir uma instância gerida usando o modelo de Gestor de Recursos Azure](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell mostra-lhe como criar e gerir uma Instância Gerida utilizando o modelo Azure PowerShell e Azure Resource Manager.|
| [Restaurar a base de dados para um caso gerido em outra geo-região](scripts/sql-managed-instance-restore-geo-backup.md) | Este script PowerShell está a pegar numa cópia de segurança de uma base de dados e a restaurá-la para outra região. Isto é conhecido como cenário de recuperação de desastres geo-restaurado. |
| **Configurar encriptação transparente de dados (TDE)**||
| [Gerencie a encriptação de dados transparente sintetizado numa instância gerida utilizando a sua própria chave do Cofre de Chaves Azure](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell configura encriptação de dados transparentes (TDE) em Bring Your Own Key Scenario for Azure SQL Managed Instance, utilizando uma chave do Cofre de Chaves Azure|
|**Configure um grupo de failover**||
| [Configure um grupo de failover para uma instância gerida](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria duas instâncias geridas, adiciona-as a um grupo de failover, e depois testa a falha da instância gerida primária para a instância gerida secundária. | 
|||

Saiba mais sobre a [Managed Instance Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Recursos adicionais

Os exemplos listados nesta página utilizam os cmdlets de base de [dados Azure SQL](/powershell/module/az.sql/) para a criação e gestão de recursos Azure SQL. Cmdlets adicionais para executar consultas, e executar muitas tarefas de base de dados estão localizados no módulo [sqlserver.](/powershell/module/sqlserver/) Para mais informações, consulte [o SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
