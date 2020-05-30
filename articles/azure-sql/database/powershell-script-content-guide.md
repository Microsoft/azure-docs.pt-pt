---
title: Exemplos de scripts Azure PowerShell
description: Exemplos de scripts Azure PowerShell para ajudá-lo a criar e gerir a Base de Dados Azure SQL e recursos de instância gerida Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 93fac2e3ae8d83f0fa49f94a3ba7880a2ecc2b4c
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193893"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Amostras Azure PowerShell para Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database e Azure SQL Managed Instance permitem-lhe configurar as suas bases de dados, instâncias e piscinas utilizando o Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar bases de dados individuais e piscinas elásticas**||
| [Crie uma única base de dados e configuure uma regra de firewall ao nível do servidor](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria uma única base de dados e configura uma regra de firewall ip nível de servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria piscinas elásticas e move bases de dados em conjunto e altera tamanhos de cálculo.|
|**Configurar georreplicação e ativação pós-falha**||
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell configura a geo-replicação ativa para uma única base de dados e falha-a na réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell configura a geo-replicação ativa para uma base de dados numa piscina elástica, e falha-a na réplica secundária. |
|**Configure um grupo de failover**||
| [Configure um grupo de failover para uma única base de dados](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria uma base de dados, e um grupo de failover adiciona a base de dados ao grupo de failover e os testes falham no servidor secundário. |
| [Configure um grupo de failover para uma piscina elástica](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria uma base de dados, adiciona-a a uma piscina elástica, adiciona a piscina elástica ao grupo de failover e os testes falham no servidor secundário. |
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell monitoriza as métricas de desempenho de uma única base de dados, escala-a para um tamanho de computação mais elevado e cria uma regra de alerta numa das métricas de desempenho. |
| [Dimensionar um conjunto elástico](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell monitoriza as métricas de desempenho de uma piscina elástica, escala-a para um tamanho de computação mais elevado, e cria uma regra de alerta numa das métricas de desempenho. |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell configura políticas de auditoria e deteção de ameaças para uma base de dados. |
| **Restaurar, copiar e importar uma base de dados**||
| [Restaurar uma base de dados](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell restaura uma base de dados a partir de uma cópia de segurança geo-redundante e restaura uma base de dados eliminada para a cópia de segurança mais recente. |
| [Copiar uma base de dados para um novo servidor](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell cria uma cópia de uma base de dados existente num novo servidor. |
| [Importar uma base de dados a partir de um ficheiro bacpac](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell importa uma base de dados para a Base de Dados Azure SQL a partir de um ficheiro bacpac. |
| **Sincronizar dados entre bases de dados**||
| [Sincronizar dados entre bases de dados SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell configura o Data Sync para sincronizar entre várias bases de dados na Base de Dados Azure SQL. |
| [Sincronizar dados entre a Base de Dados SQL e o SqL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell configura o Data Sync para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados do SQL Server. |
| [Atualizar o esquema de Sincronização de Dados SQL](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell adiciona ou remove itens do esquema de Sincronização de Dados. |
|||

Saiba mais sobre a [API AZure PowerShell da Base de Dados Única](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Instância Gerida do SQL no Azure](#tab/managed-instance)

A tabela seguinte inclui links para amostrar scripts Azure PowerShell para Azure SQL Managed Instance.

| |  |
|---|---|
|**Criar e configurar instâncias geridas**||
| [Criar e gerir um caso gerido](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Este script PowerShell mostra-lhe como criar e gerir um caso gerido usando o Azure PowerShell |
| [Criar e gerir um caso gerido usando o modelo do Gestor de Recursos Azure](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell mostra-lhe como criar e gerir um caso gerido usando o modelo Azure PowerShell e Azure Resource Manager.|
| [Restaurar a base de dados para um caso gerido em outra Geo-região](../managed-instance/scripts/restore-geo-backup.md) | Este script PowerShell está a fazer uma cópia de segurança de uma base de dados e a restaurá-la para outra região. Isto é conhecido como cenário de recuperação de desastres Geo-Restaurar. |
| **Configure encriptação de dados transparentes (TDE)**||
| [Gerir a Encriptação de Dados Transparentes num caso gerido usando a sua própria chave a partir do Cofre de Chaves Azure](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script PowerShell configura a encriptação de dados transparente (TDE) em Bring Your Own Key scenario for Azure SQL Managed Instance, usando uma chave do Cofre de Chaves Azure|
|**Configure um grupo de failover**||
| [Configure um grupo de failover para um caso gerido](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script PowerShell cria duas instâncias geridas, adiciona-as a um grupo de failover, e depois testa o failover da instância gerida primária para o caso secundário gerido. |
|||

Saiba mais sobre [os cmdlets PowerShell para Azure SQL Managed Instance](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Recursos adicionais

Os exemplos listados nesta página utilizam os [cmdlets PowerShell](/powershell/module/az.sql/) para criar e gerir os recursos Azure SQL. Os cmdlets adicionais para a execução de consultas e a realização de muitas tarefas de base de dados estão localizados no módulo [sqlserver.](/powershell/module/sqlserver/) Para obter mais informações, consulte [o SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
