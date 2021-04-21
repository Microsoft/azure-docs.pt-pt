---
title: Referência da API de gestão para Azure SQL Caso Gerido
description: Saiba como criar e configurar instâncias geridas de Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784376"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Referência de API gerida para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode criar e configurar instâncias geridas de Azure SQL Gestd Instance utilizando o portal Azure, PowerShell, Azure CLI, REST API e Transact-SQL. Neste artigo, pode encontrar uma visão geral das funções e da API que pode utilizar para criar e configurar casos geridos.

## <a name="azure-portal-create-a-managed-instance"></a>Portal Azure: Criar uma instância gerida

Para um arranque rápido mostrando-lhe como criar um exemplo gerido, consulte [Quickstart: Criar um exemplo gerido](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Criar e configurar instâncias geridas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRM são substancialmente idênticos.

Para criar e gerir casos geridos com a Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se necessitar de instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo PowerShell, consulte [o script Quickstart: Crie uma instância gerida utilizando uma biblioteca PowerShell](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Cria um caso gerido. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Devolve informações sobre um caso gerido.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para uma instância gerida.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Remove um caso gerido.|
|[Get-AzSqlInstanceOperação](/powershell/module/az.sql/get-azsqlinstanceoperation)|Obtém uma lista de operações de gestão realizadas na instância gerida ou operação específica.|
|[Stop-AzSqlInstanceOperação](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Cancela a operação de gestão específica realizada na instância gerida.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria uma base de dados SQL Managed Instance.|
|[Sebase de Dados Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstancedatabase)|Devolve informações sobre uma base de dados SQL Managed Instance.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove uma base de dados SQL Managed Instance.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura uma base de dados SQL Managed Instance.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Criar e configurar instâncias geridas

Para criar e configurar casos geridos com [o Azure CLI,](/cli/azure)utilize os [seguintes comandos Azure CLI para sql Managed Instance](/cli/azure/sql/mi). Utilize [o Azure Cloud Shell](../../cloud-shell/overview.md) para executar o CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) em macOS, Linux ou Windows.

> [!TIP]
> Para um quickstart Azure CLI, consulte [Working with SQL Managed Instance usando Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi criar](/cli/azure/sql/mi#az_sql_mi_create) |Cria um caso gerido.|
|[lista az sql mi](/cli/azure/sql/mi#az_sql_mi_list)|Listas disponíveis casos geridos.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|Obtém os detalhes para um caso gerido.|
|[az sql mi atualização](/cli/azure/sql/mi#az_sql_mi_update)|Atualiza um caso gerido.|
|[az sql mi apagar](/cli/azure/sql/mi#az_sql_mi_delete)|Remove um caso gerido.|
|[az sql mi op lista](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Obtém uma lista de operações de gestão realizadas na instância gerida.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Obtém a operação de gestão específica realizada na instância gerida.|
|[az sql mi op cancelar](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Cancela a operação de gestão específica realizada na instância gerida.|
|[az sql midb criar](/cli/azure/sql/midb#az_sql_midb_create) |Cria uma base de dados gerida.|
|[az sql midb lista](/cli/azure/sql/midb#az_sql_midb_list)|Listas disponíveis bases de dados geridas.|
|[az sql midb restaurar](/cli/azure/sql/midb#az_sql_midb_restore)|Restaura uma base de dados gerida.|
|[az sql midb apagar](/cli/azure/sql/midb#az_sql_midb_delete)|Remove uma base de dados gerida.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Criar e configurar bases de dados de casos

Para criar e configurar bases de dados de casos após a criação da instância gerida, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), Visual Studio [Code,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor e passar comandos Transact-SQL.

> [!TIP]
> Para arranques rápidos que lhe mostrem como configurar e ligar a uma instância gerida utilizando o SQL Server Management Studio no Microsoft Windows, consulte [Quickstart: Configure Azure VM para ligar a Azure SQL Managed Instance](connect-vm-instance-configure.md) e [Quickstart: Configure uma ligação ponto-a-local ao Azure SQL Managed Instance a partir de instalações](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Não é possível criar ou eliminar um caso gerido utilizando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Cria uma nova base de dados de casos em SQL Managed Instance. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Modifica uma base de dados de casos em SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: Criar e configurar instâncias geridas

Para criar e configurar casos geridos, utilize estes pedidos de API REST.

| Comando | Descrição |
| --- | --- |
|[Casos geridos - Criar ou Atualizar](/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerida.|
|[Casos geridos - Eliminar](/rest/api/sql/managedinstances/delete)|Elimina um caso gerido.|
|[Casos Geridos - Obter](/rest/api/sql/managedinstances/get)|Obtém um caso gerido.|
|[Casos Geridos - Lista](/rest/api/sql/managedinstances/list)|Devolve uma lista de casos geridos numa subscrição.|
|[Instâncias geridas - Lista por Grupo de Recursos](/rest/api/sql/managedinstances/listbyresourcegroup)|Devolve uma lista de casos geridos num grupo de recursos.|
|[Casos Geridos - Atualização](/rest/api/sql/managedinstances/update)|Atualiza um caso gerido.|
|[Operações de instância geridas - Lista por Instância Gerida](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Obtém uma lista de operações de gestão realizadas na instância gerida.|
|[Operações de instância geridas - Obter](/rest/api/sql/managedinstanceoperations/get)|Obtém a operação de gestão específica realizada na instância gerida.|
|[Operações de Ocorrência Gerida - Cancelar](/rest/api/sql/managedinstanceoperations/cancel)|Cancela a operação de gestão específica realizada na instância gerida.|

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a migração de uma base de dados do SQL Server para Azure, consulte [a Migração para a Base de Dados SQL Azure](../database/migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](../database/features-comparison.md).