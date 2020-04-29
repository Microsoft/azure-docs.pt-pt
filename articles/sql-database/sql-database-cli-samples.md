---
title: Exemplos de script sinuoso azure CLI
description: Exemplos de script do Azure CLI para criar e gerir servidores da Base de Dados SQL do Azure, conjuntos elásticos, bases de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061707"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Amostras do Azure CLI para a Base de Dados SQL do Azure

A base de dados Azure SQL pode ser configurada utilizando <a href="/cli/azure">o Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Base de dados única & piscinas elásticas](#tab/single-database)

| | |
|---|---|
|**Criar uma base de dados e um conjunto elástico**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md) | Cria uma base de dados Azure SQL e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/sql-database-move-database-between-pools-cli.md) | Cria piscinas elásticas SQL, move bases de dados Azure SQL e altera tamanhos de computação. |
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-cli.md) | Dimensiona uma base de dados Azure SQL para um tamanho de computação diferente depois de consultar a informação de tamanho para a base de dados. |
| [Dimensionar um conjunto elástico](scripts/sql-database-scale-pool-cli.md) | Escala uma piscina elástica SQL para um tamanho de computação diferente. |
|**Configurar georreplicação e ativação pós-falha**||
| [Adicione uma única base de dados ao grupo failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Cria uma base de dados e um grupo de failover, adiciona a base de dados ao grupo failover, em seguida, testa falha no servidor secundário. |
| [Configure um grupo de failover para uma piscina elástica](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Cria uma base de dados, adiciona-a a uma piscina elástica, adiciona a piscina elástica ao grupo failover, depois testa falha no servidor secundário. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura a geo-replicação ativa para uma base de dados Azure SQL e falha-a na réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura a geo-replicação ativa para uma base de dados Azure SQL numa piscina elástica SQL, falhando-a na réplica secundária. |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](scripts/sql-database-auditing-and-threat-detection-cli.md)| Configures políticas de auditoria e deteção de ameaças para uma base de dados Azure SQL. |
| **Backup, restaurar, copiar e importar uma base de dados**||
| [Backup de uma base de dados](scripts/sql-database-backup-database-cli.md)| Faça um backup de base Azure SQL para um depósito de armazenamento Azure. |
| [Restaurar uma base de dados](scripts/sql-database-restore-database-cli.md)| Restaura uma base de dados Azure SQL a partir de uma cópia de segurança georedundante e restaura uma base de dados Azure SQL eliminada para a mais recente cópia de segurança. |
| [Copiar uma base de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-cli.md) | Cria uma cópia de uma base de dados Azure SQL existente num novo servidor Azure SQL. |
| [Importar uma base de dados de um ficheiro bacpac](scripts/sql-database-import-from-bacpac-cli.md)| Importa uma base de dados para um servidor Azure SQL a partir de um ficheiro *.bacpac.* |
|||

Saiba mais sobre a base de [dados única Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Instância gerida](#tab/managed-instance)

A tabela seguinte inclui links para exemplos de scripts Azure CLI para Base de Dados Azure SQL - Instância Gerida.

| | |
|---|---|
| **Criar uma Instância Gerida**||
| [Criar um caso gerido](scripts/sql-database-create-configure-managed-instance-cli.md)| Cria um caso gerido. |
| **Configurar encriptação transparente de dados (TDE)**||
| [Gerir encriptação de dados transparente em uma instância gerida usando cofre de chave Azure](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configures A Encriptação transparente de dados (TDE) em 1º Caso Gerido Azure SQL utilizando o Cofre chave Azure com vários cenários-chave. |
|**Configure um grupo de failover**||
| [Configure um grupo de failover para uma instância gerida](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Cria duas instâncias geridas, adiciona-as a um grupo de failover, depois testa a falha da instância gerida primária para a instância gerida secundária. |
|||

Para obter exemplos adicionais de Exemplo Gerido, consulte a [criação](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [atualização,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) [mova uma base de dados,](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) [trabalhando com](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) scripts.

Saiba mais sobre a [instância gerida Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
