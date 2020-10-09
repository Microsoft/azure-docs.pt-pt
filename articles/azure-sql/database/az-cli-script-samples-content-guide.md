---
title: Exemplos de scripts do Azure CLI
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exemplos de scripts Azure CLI para criar e gerir a Base de Dados Azure SQL e Azure SQL Gestão de Instâncias
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 893ea607b322c2f9b2d8a4e8b5b087e21c476d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497176"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Amostras de Azure CLI para Azure SQL Database e SQL Managed Instance 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pode configurar a Base de Dados Azure SQL e a SQL Managed Instance utilizando o <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Base de Dados SQL do Azure](#tab/single-database)

A tabela seguinte inclui links para exemplos de scripts Azure CLI para gerir bases de dados individuais e aginhadas na Base de Dados Azure SQL. 

|Área|Descrição|
|---|---|
|**Criar bases de dados na Base de Dados Azure SQL**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/create-and-configure-database-cli.md) | Cria uma Base de Dados SQL e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/move-database-between-elastic-pools-cli.md) | Cria piscinas elásticas, move bases de dados agrizadas e altera tamanhos de cálculo. |
|**Bases de dados de escala na Base de Dados Azure SQL**||
| [Dimensionar uma base de dados](scripts/monitor-and-scale-database-cli.md) | Escala uma base de dados na Base de Dados SQL para um tamanho de cálculo diferente depois de consultar as informações de tamanho para a base de dados. |
| [Dimensionar um conjunto elástico](scripts/scale-pool-cli.md) | Dimensiona uma piscina elástica SQL para um tamanho computacional diferente. |
|**Configurar georreplicação e ativação pós-falha**||
| [Adicione uma única base de dados a um grupo de failover](scripts/add-database-to-failover-group-cli.md)| Cria uma base de dados e um grupo de failover, adiciona a base de dados ao grupo de failover e, em seguida, os testes falham no servidor secundário. |
| [Configure um grupo de failover para uma piscina elástica](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Cria uma base de dados, adiciona-a a uma piscina elástica, adiciona a piscina elástica ao grupo de failover e, em seguida, testa falha no servidor secundário. |
| [Configurar e falhar sobre uma única base de dados utilizando a geo-replicação ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura a geo-replicação ativa para uma base de dados na Base de Dados Azure SQL e falha-a na réplica secundária. |
| [Configurar e falhar sobre uma base de dados agimiutada utilizando a geo-replicação ativa](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura a geo-replicação ativa para uma base de dados numa piscina elástica, e depois falha-a na réplica secundária. |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Configura políticas de auditoria e deteção de ameaças para uma base de dados na Base de Dados Azure SQL. |
| **Fazer back-up, restaurar, copiar e importar uma base de dados**||
| [Fazer o back-up de uma base de dados](../../sql-database/scripts/sql-database-backup-database-cli.md)| Confirma uma base de dados na Base de Dados SQL para uma cópia de segurança de armazenamento Azure. |
| [Restaurar uma base de dados](../../sql-database/scripts/sql-database-restore-database-cli.md)| Restaura uma base de dados na Base de Dados SQL a partir de uma cópia de segurança geo-redundante e restaura uma base de dados eliminada para a cópia de segurança mais recente. |
| [Copiar uma base de dados para um novo servidor](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Cria uma cópia de uma base de dados existente na Base de Dados SQL num novo servidor. |
| [Importar uma base de dados de um ficheiro BACPAC](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importa uma base de dados para a Base de Dados SQL a partir de um ficheiro BACPAC. |
|||

Saiba mais sobre a [AZure CLI API de uma única base de dados.](single-database-manage.md#the-azure-cli)

# <a name="azure-sql-managed-instance"></a>[Instância Gerida do SQL no Azure](#tab/managed-instance)

A tabela seguinte inclui links para exemplos de scripts Azure CLI para Azure SQL Managed Instance.

|Área|Descrição|
|---|---|
| **Criar uma sql gestd managing instance**||
| [Criar uma sql gestd managing instance](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Cria uma SQL Managed Instance. |
| **Configure encriptação de dados transparentes (TDE)**||
| [Gerir encriptação de dados transparentes em uma sql managed instance usando Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configura a Encriptação de Dados Transparente (TDE) em SQL Managed Instance utilizando o Cofre da Chave Azure com vários cenários-chave. |
|**Configure um grupo de failover**||
| [Configure um grupo de failover para a SQL Managed Instance](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Cria duas instâncias de SQL Managed Instance, adiciona-as a um grupo de failover, e depois testa o failover da primeira SQL Managed Instance para a segunda SQL Managed Instance. |
|||

Para exemplos adicionais de SQL Managed Instance, consulte a [criação,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) [atualização,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) [mova uma base de dados](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)e [trabalhe com](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts.

Saiba mais sobre a [SQL Managed Instance Azure CLI API](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---
