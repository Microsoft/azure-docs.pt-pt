---
title: Exemplos de script sinuoso azure CLI
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exemplos de script séc. Azure CLI para criar e gerir base de dados Azure SQL e Instância Gerida Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: f5a8e5c9af9f3dc27b517c386c4abae24dc45239
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053418"
---
# <a name="azure-cli-samples-for-azure-sql-database--sql-managed-instance"></a>Amostras azure CLI para base de dados Azure SQL & Instância Gerida SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Base de Dados Azure SQL e a Instância Gerida SQL podem ser configuradas utilizando <a href="/cli/azure">o Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

A tabela seguinte inclui links para os exemplos de scripts Do ClI Azure para gerir bases de dados únicas e reunidas na Base de Dados Azure SQL. 

| | |
|---|---|
|**Criar uma Base de Dados SQL do Azure**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/create-and-configure-database-cli.md) | Cria uma Base de Dados SQL e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/move-database-between-elastic-pools-cli.md) | Cria piscinas elásticas, move bases de dados SQL reunidas e altera tamanhos de computação. |
|**Base de Dados Scale Azure SQL**||
| [Dimensionar uma base de dados](scripts/monitor-and-scale-database-cli.md) | Dimensiona uma Base de Dados SQL para um tamanho de cálculo diferente depois de consultar a informação de tamanho para a base de dados. |
| [Dimensionar um conjunto elástico](scripts/scale-pool-cli.md) | Escala uma piscina elástica SQL para um tamanho de computação diferente. |
|**Configurar georreplicação e ativação pós-falha**||
| [Adicione uma única base de dados ao grupo failover](scripts/add-database-to-failover-group-cli.md)| Cria uma base de dados e um grupo de failover, adiciona a base de dados ao grupo failover, em seguida, testa falha no servidor secundário. |
| [Configure um grupo de failover para uma piscina elástica](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Cria uma base de dados, adiciona-a a uma piscina elástica, adiciona a piscina elástica ao grupo failover, depois testa falha no servidor secundário. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura a geo-replicação ativa para uma base de dados Azure SQL e falha-a na réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura a geo-replicação ativa para uma base de dados Azure SQL numa piscina elástica SQL, falhando-a na réplica secundária. |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Configures políticas de auditoria e deteção de ameaças para uma base de dados Azure SQL. |
| **Backup, restaurar, copiar e importar uma base de dados**||
| [Backup de uma base de dados](../../sql-database/scripts/sql-database-backup-database-cli.md)| Recue uma base de dados SQL para uma cópia de segurança do Azure. |
| [Restaurar uma base de dados](../../sql-database/scripts/sql-database-restore-database-cli.md)| Restaura uma base de dados SQL a partir de uma cópia de segurança georedundante e restaura uma base de dados SQL eliminada para a última cópia de segurança. |
| [Copiar uma base de dados para um novo servidor](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Cria uma cópia de uma base de dados SQL existente num novo servidor. |
| [Importar uma base de dados de um ficheiro bacpac](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importa uma base de dados para a Base de Dados SQL a partir de um ficheiro *.bacpac.* |
|||

Saiba mais sobre a base de [dados única Azure CLI API](single-database-manage.md#azure-cli).

# <a name="azure-sql-managed-instance"></a>[Instância Gerida do Azure SQL](#tab/managed-instance)

A tabela seguinte inclui links para exemplos de scripts Azure CLI para A Instância Gerida Azure SQL.

| | |
|---|---|
| **Criar uma instância gerida por SQL**||
| [Criar uma instância gerida por SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Cria uma instância gerida por SQL. |
| **Configurar encriptação transparente de dados (TDE)**||
| [Gerir encriptação transparente de dados em uma instância gerida SQL usando cofre de chave Azure](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configures A Encriptação transparente de dados (TDE) em Instância Gerida SQL utilizando o Cofre chave Azure com vários cenários-chave. |
|**Configure um grupo de failover**||
| [Configure um grupo de failover para SQL Managed Instance](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Cria duas Instâncias Geridas SQL, adiciona-as a um grupo de failover, depois testa a falha da instância gerida primária sQL para a instância gerida secundária sQL. |
|||

Para obter exemplos adicionais da SQL Managed Instance, consulte a [criação](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [a atualização,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) [o movimento de uma base de dados,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) [trabalhando com](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts.

Saiba mais sobre a [SQL Managed Instance Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-manage-managed-instances).

---
