---
title: Exemplos de script do Azure CLI para a Base de Dados SQL | Microsoft Docs
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
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: ad4706ce038feb316d238c1cabc12b27621c8085
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443337"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Amostras do Azure CLI para a Base de Dados SQL do Azure

O banco de dados SQL do Azure pode ser configurado usando <a href="/cli/azure">CLI do Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-pools"></a>& Pools elásticos de banco de dados individual

A tabela seguinte inclui ligações para exemplos de script do Azure CLI para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar uma base de dados e um conjunto elástico**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados de conjunto](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI cria conjuntos elásticos SQL, move bases de dados agrupadas do Azure SQL e altera os tamanhos da computação.|
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI dimensiona uma base de dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho da base de dados. |
| [Dimensionar um conjunto elástico](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script CLI dimensiona um conjunto elástico de SQL para um tamanho da computação diferente.  |
|**Grupos de failover**||
| [Adicionar Banco de dados individual ao grupo de failover](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Esse script de CLI cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover para o servidor secundário.|
|||

Saiba mais sobre a [API de CLI do Azure de banco de dados individual](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instância Gerida

A tabela a seguir inclui links para CLI do Azure exemplos de script para o banco de dados SQL do Azure-Instância Gerenciada.

| |  |
|---|---|
| [Criar uma Instância Gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Este script da CLI mostra como criar um Instância Gerenciada. |
| [Atualizar um Instância Gerenciada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Este script da CLI mostra como atualizar um Instância Gerenciada. |
| [Mover um banco de dados para outro Instância Gerenciada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Este script da CLI mostra como restaurar um backup de um banco de dados de uma instância do para outra. |
|||

Saiba mais sobre a [API de CLI do Azure de instância gerenciada](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) e encontre [exemplos adicionais aqui](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
