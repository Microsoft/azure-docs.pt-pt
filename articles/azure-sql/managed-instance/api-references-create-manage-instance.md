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
ms.openlocfilehash: 2b98b11d85bde8dd49b6bbde5d048493493b73f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620244"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Referência de API gerida para o Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode criar e configurar instâncias geridas de Azure SQL Gestd Instance utilizando o portal Azure, PowerShell, Azure CLI, REST API e Transact-SQL. Neste artigo, pode encontrar uma visão geral das funções e da API que pode utilizar para criar e configurar casos geridos.

## <a name="azure-portal-create-a-managed-instance"></a>Portal Azure: Criar uma instância gerida

Para um arranque rápido mostrando-lhe como criar um exemplo gerido, consulte [Quickstart: Criar um exemplo gerido](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Criar e configurar instâncias geridas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRM são substancialmente idênticos.

Para criar e gerir casos geridos com a Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se necessitar de instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo PowerShell, consulte [o script Quickstart: Crie uma instância gerida utilizando uma biblioteca PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria um caso gerido. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Devolve informações sobre um caso gerido.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para uma instância gerida.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove um caso gerido.|
|[Get-AzSqlInstanceOperação](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)|Obtém uma lista de operações de gestão realizadas na instância gerida ou operação específica.|
|[Stop-AzSqlInstanceOperação](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)|Cancela a operação de gestão específica realizada na instância gerida.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria uma base de dados SQL Managed Instance.|
|[Sebase de Dados Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Devolve informações sobre uma base de dados SQL Managed Instance.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove uma base de dados SQL Managed Instance.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura uma base de dados SQL Managed Instance.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Criar e configurar instâncias geridas

Para criar e configurar casos geridos com [o Azure CLI,](/cli/azure)utilize os [seguintes comandos Azure CLI para sql Managed Instance](/cli/azure/sql/mi). Utilize [o Azure Cloud Shell](/azure/cloud-shell/overview) para executar o CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) em macOS, Linux ou Windows.

> [!TIP]
> Para um quickstart Azure CLI, consulte [Working with SQL Managed Instance usando Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi criar](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria um caso gerido.|
|[lista az sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Listas disponíveis casos geridos.|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtém os detalhes para um caso gerido.|
|[az sql mi atualização](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza um caso gerido.|
|[az sql mi apagar](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove um caso gerido.|
|[az sql mi op lista](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_list)|Obtém uma lista de operações de gestão realizadas na instância gerida.|
|[az sql mi op show](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_show)|Obtém a operação de gestão específica realizada na instância gerida.|
|[az sql mi op cancelar](https://docs.microsoft.com/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Cancela a operação de gestão específica realizada na instância gerida.|
|[az sql midb criar](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria uma base de dados gerida.|
|[az sql midb lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Listas disponíveis bases de dados geridas.|
|[az sql midb restaurar](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaura uma base de dados gerida.|
|[az sql midb apagar](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove uma base de dados gerida.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Criar e configurar bases de dados de casos

Para criar e configurar bases de dados de casos após a criação da instância gerida, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), Visual Studio [Code,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor e passar comandos Transact-SQL.

> [!TIP]
> Para arranques rápidos que lhe mostrem como configurar e ligar a uma instância gerida utilizando o SQL Server Management Studio no Microsoft Windows, consulte [Quickstart: Configure Azure VM para ligar a Azure SQL Managed Instance](connect-vm-instance-configure.md) e [Quickstart: Configure uma ligação ponto-a-local ao Azure SQL Managed Instance a partir de instalações](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Não é possível criar ou eliminar um caso gerido utilizando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CRIAR BASE DE DADOs](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true)|Cria uma nova base de dados de casos em SQL Managed Instance. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |Modifica uma base de dados de casos em SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: Criar e configurar instâncias geridas

Para criar e configurar casos geridos, utilize estes pedidos de API REST.

| Comando | Descrição |
| --- | --- |
|[Casos geridos - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerida.|
|[Casos geridos - Eliminar](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Elimina um caso gerido.|
|[Casos Geridos - Obter](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém um caso gerido.|
|[Casos Geridos - Lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Devolve uma lista de casos geridos numa subscrição.|
|[Instâncias geridas - Lista por Grupo de Recursos](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Devolve uma lista de casos geridos num grupo de recursos.|
|[Casos Geridos - Atualização](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza um caso gerido.|
|[Operações de instância geridas - Lista por Instância Gerida](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Obtém uma lista de operações de gestão realizadas na instância gerida.|
|[Operações de instância geridas - Obter](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|Obtém a operação de gestão específica realizada na instância gerida.|
|[Operações de Ocorrência Gerida - Cancelar](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|Cancela a operação de gestão específica realizada na instância gerida.|

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a migração de uma base de dados do SQL Server para Azure, consulte [a Migração para a Base de Dados SQL Azure](../database/migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](../database/features-comparison.md).
