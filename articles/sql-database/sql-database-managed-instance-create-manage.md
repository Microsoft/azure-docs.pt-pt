---
title: Referência de API de gestão para a instância gerida da base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre criar e gerir instâncias geridas da base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 852f181ff0bb84bc3bcdb6d674ecf72eca900d7d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312584"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Referência da API gerida para instâncias geridas da base de dados SQL do Azure

Pode criar e gerenciar instâncias do Azure SQL da base de dados geridos com o portal do Azure, PowerShell, CLI do Azure, REST API e Transact-SQL. Neste artigo, pode encontrar uma descrição geral das funções e API que pode utilizar para criar e configurar a instância gerida.

## <a name="azure-portal-create-a-managed-instance"></a>Portal do Azure: Criar uma instância gerida

Para um início rápido mostra-lhe como criar uma instância de gerida de base de dados do Azure SQL, consulte [início rápido: Criar uma instância gerida de base de dados SQL do Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Criar e gerir instâncias geridas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar e gerir instâncias geridas com o Azure PowerShell, utilize os seguintes cmdlets do PowerShell. Se precisar de instalar ou atualizar o PowerShell, veja [módulo de instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [script de início rápido: Criar instância de gerida de SQL em Azure usando a biblioteca do PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria uma instância gerida de base de dados SQL do Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Devolve informações sobre a instância gerida do SQL do Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define as propriedades para uma instância de gerida de base de dados do Azure SQL|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove uma instância de base de dados SQL do Azure|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria uma base de dados de instância gerida da base de dados SQL do Azure|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Devolve informações sobre a base de dados de instância gerida do SQL do Azure|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove uma base de dados de instância de base de dados gerida do Azure SQL|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura uma base de dados de instância de base de dados gerida do Azure SQL|

## <a name="azure-cli-create-and-manage-managed-instances"></a>CLI do Azure: Criar e gerir instâncias geridas

Para criar e gerir instâncias geridas com [CLI do Azure](/cli/azure), utilize o seguinte procedimento [instância gerida SQL da CLI do Azure](/cli/azure/sql/mi) comandos. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para um guia de introdução do CLI do Azure, consulte [trabalhar com a instância gerida de SQL com a CLI do Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria uma instância gerida|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Listas de instâncias de geridas disponíveis|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtenha os detalhes para uma instância gerida|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualizações de uma instância gerida|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove uma instância gerida|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria uma base de dados gerida|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Bases de dados geridas de listas disponíveis|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaurar uma base de dados gerida|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove uma base de dados gerida|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Criar e gerir bases de dados de instância

Para criar e gerir a base de dados de instância depois da instância gerida é criada, utilize os seguintes comandos T-SQL. Pode emitir estes comandos no portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Estúdio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que pode ligar a um servidor de base de dados do Azure SQL e passar comandos Transact-SQL.

> [!TIP]
> Para guias de início rápido que mostra de tem de configurar e ligar a uma instância gerida com o SQL Server Management Studio no Microsoft Windows, consulte [início rápido: Configurar a VM do Azure para ligar a um Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) e [início rápido: Configurar uma ligação de ponto a site para uma instância de gerida de base de dados do Azure SQL no local](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Não é possível criar ou eliminar uma instância gerida com o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria uma nova base de dados de instância gerida. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica uma base de dados de instância gerida do SQL do Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Criar e gerir instâncias geridas

Para criar e gerir instâncias geridas, utilize estes pedidos de REST API.

| Comando | Descrição |
| --- | --- |
|[Instâncias - geridas criar ou atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerida.|
|[Instâncias geridas - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Elimina uma instância gerida.|
|[Instâncias geridas - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém uma instância gerida.|
|[Lista de instâncias - geridas](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Devolve uma lista de instâncias geridas numa subscrição.|
|[Instâncias - lista por grupo de recursos geridas](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Devolve uma lista de instâncias geridas num grupo de recursos.|
|[Instâncias geridas - atualização](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza uma instância gerida.|

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como migrar uma base de dados do SQL Server para o Azure, veja [migre para o Azure SQL Database](sql-database-single-database-migrate.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).
