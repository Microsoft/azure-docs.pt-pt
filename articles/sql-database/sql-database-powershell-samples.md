---
title: Exemplos de scripts do Azure PowerShell para a Base de Dados SQL | Microsoft Docs
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
ms.openlocfilehash: 27b4a6f5614b79b2e9d8f3730441d702f2d7cc77
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933263"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemplos do Azure PowerShell para a Base de Dados SQL do Azure

O banco de dados SQL do Azure permite que você configure seus bancos de dados, instâncias e pools usando Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá AZ PowerShell 1.4.0 ou posterior. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="single-database-and-elastic-poolstabsingle-database"></a>[Pools elásticos e Banco de Dados Individual](#tab/single-database)

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar bancos de dados individuais e pools elásticos**||
| [Criar um banco de dados individual e configurar uma regra de firewall do servidor de banco de dados](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria conjuntos elásticos da Base de Dados SQL do Azure, move bases de dados agrupadas e altera os tamanhos da computação.|
|**Configurar georreplicação e ativação pós-falha**||
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure num conjunto elástico SQL e realiza a ativação pós-falha para uma réplica secundária. |
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esse script do PowerShell cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover para o servidor secundário. | 
| [Configurar um grupo de failover para um pool elástico](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esse script do PowerShell cria um banco de dados, adiciona-o a um pool elástico, adiciona o pool elástico ao grupo de failover e testa o failover para o servidor secundário. | 
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

Saiba mais sobre a [API de Azure PowerShell de banco de dados individual](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Instância Gerenciada](#tab/managed-instance)

A tabela a seguir inclui links para scripts de Azure PowerShell de exemplo para o banco de dados SQL do Azure-Instância Gerenciada.

| |  |
|---|---|
|**Criar e configurar instâncias gerenciadas**||
| [Criar e gerir uma Instância Gerida](scripts/sql-database-create-configure-managed-instance-powershell.md) | Este script do PowerShell mostra como criar e gerir uma Instância Gerida com o Azure PowerShell |
| [Criar e gerenciar um Instância Gerenciada usando o modelo Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell mostra como criar e gerenciar um Instância Gerenciada usando o Azure PowerShell e o modelo de Azure Resource Manager.|
| [Restaurar o banco de dados para um Instância Gerenciada em outra região geográfica](scripts/sql-managed-instance-restore-geo-backup.md) | Este script do PowerShell está fazendo um backup de um banco de dados e o restaura em outra região. Isso é conhecido como cenário de recuperação de desastre de restauração geográfica. |
| **Configurar Transparent Data Encryption (TDE)**||
| [Gerenciar Transparent Data Encryption em um Instância Gerenciada usando sua própria chave de Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura o Transparent Data Encryption (TDE) no cenário Bring Your Own Key para Instância Gerenciada do SQL do Azure, usando uma chave de Azure Key Vault|
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para uma instância gerenciada](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Esse script do PowerShell cria duas instâncias gerenciadas, adiciona-as a um grupo de failover e, em seguida, testa o failover da instância gerenciada primária para a instância gerenciada secundária. | 
|||

Saiba mais sobre a [API de Azure PowerShell de instância gerenciada](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Recursos adicionais

Os exemplos listados nesta página usam os [cmdlets do banco de dados SQL do Azure](/powershell/module/az.sql/) para criar e gerenciar recursos do SQL do Azure. Os cmdlets adicionais para executar consultas e executar muitas tarefas de banco de dados estão localizados no módulo [SqlServer](/powershell/module/sqlserver/) . Para obter mais informações, consulte [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
