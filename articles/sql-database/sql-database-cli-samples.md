---
title: Exemplos de script do Azure CLI para a Base de Dados SQL | Microsoft Docs
description: Exemplos de script do Azure CLI para criar e gerir servidores de Base de Dados SQL do Azure, conjuntos elásticos, bases de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: 74d322c9e7d45a26f9e095bd7ae9f2bbe506ba49
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306940"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Amostras do Azure CLI para a Base de Dados SQL do Azure

Base de dados SQL do Azure pode ser configurado usando <a href="/cli/azure">CLI do Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="single-database--elastic-pools"></a>Base de dados e conjuntos elásticos

A tabela seguinte inclui ligações para exemplos de script do Azure CLI para a Base de Dados do Azure SQL.

| |  |
|---|---|
|**Criar uma base de dados e um conjunto elástico**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI cria uma base de dados do Azure SQL e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados agrupadas](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI cria conjuntos elásticos SQL, move bases de dados agrupadas do Azure SQL e altera os tamanhos de computação.|
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script de CLI dimensiona uma base de dados SQL do Azure para um tamanho de computação diferente depois de consultar as informações de tamanho da base de dados. |
| [Dimensionar um conjunto elástico](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script CLI dimensiona um conjunto elástico de SQL para um tamanho de computação diferente.  |
|||

Saiba mais sobre o [única base de dados do Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instância Gerida

A tabela seguinte inclui ligações para exemplos de script da CLI do Azure para o Azure SQL Database - instância gerida.

| |  |
|---|---|
| [Criar uma Instância Gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Este script da CLI mostra como criar uma instância gerida. |
| [Atualizar uma instância gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Este script da CLI mostra como atualizar uma instância gerida. |
| [Mover uma base de dados para outra instância gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Este script da CLI mostra como restaurar uma cópia de segurança das bases de dados de uma instância para outro. |
|||

Saiba mais sobre o [API de CLI do Azure de instância gerida](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) e localize [exemplos adicionais aqui](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
