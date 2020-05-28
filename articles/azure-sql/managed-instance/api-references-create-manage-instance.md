---
title: Referência da API de gestão para instância gerida
description: Aprenda sobre a criação e gestão de casos geridos pelo Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ca7e123a3a0a1707eb979f25cabfc5913c5bfcb8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046321"
---
# <a name="managed-api-reference-for-azure-sql-managed-instances"></a>Referência gerida aPI para instâncias geridas pelo Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pode criar e gerir instâncias geridas azure SQL utilizando o portal Azure, PowerShell, Azure CLI, REST API e Transact-SQL. Neste artigo, pode encontrar uma visão geral das funções e da API que pode utilizar para criar e configurar a Instância Gerida.

## <a name="azure-portal-create-a-managed-instance"></a>Portal Azure: Criar uma instância gerida

Para um início rápido mostrando-lhe como criar uma instância gerida por base de dados SQL, consulte [Quickstart: Create a SQL Managed Instance](instance-create-quickstart.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Criar e gerir casos geridos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerir casos geridos com o Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte instalar o [módulo PowerShell Azure](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo powerShell, consulte [o script Quick-start: Create SQL Managed Instance utilizando a biblioteca PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria uma instância gerida azure SQL |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Devolve informações sobre a Instância Gerida Azure SQL|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para um Caso Gerido Azure SQL|
|[Remover-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove uma instância de base de dados gerida azure SQL|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria uma base de dados azure SQL Managed Instance|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Devolve informações sobre a base de dados Azure SQL Managed Instance|
|[Remover-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove uma base de dados de instâncias de dados gerida sql azure|
|[Restaurar-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura uma base de dados de instâncias de dados gerida sql Azure SQL|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Criar e gerir casos geridos

Para criar e gerir casos geridos com [o Azure CLI,](/cli/azure)utilize os seguintes comandos [Azure CLI SQL Managed Instance.](/cli/azure/sql/mi) Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para um quickstart Azure CLI, consulte [Trabalhar com a SQL Managed Instance utilizando o Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi criar](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria uma instância gerida|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Listas disponíveis Casos Geridos|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtenha os detalhes para um Caso Gerido|
|[az sql mi atualização](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza ções de uma instância gerida|
|[az sql mi excluir](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove uma instância gerida|
|[az sql midb criar](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria uma base de dados gerida|
|[az sql midb lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Listas disponíveis de bases de dados geridas|
|[az sql midb restaurar](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaurar uma base de dados gerida|
|[az sql midb excluir](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove uma base de dados gerida|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Criar e gerir bases de dados de instâncias

Para criar e gerir a base de dados de instâncias após a criação da Instância Gerida, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que possa ligar-se a um servidor e passar comandos Transact-SQL.

> [!TIP]
> Para começar rapidamente mostrando que tem de configurar e ligar-se a uma instância gerida pela SQL utilizando o Estúdio de Gestão de Servidores SQL no Microsoft Windows, consulte [Quickstart: Configure Azure VM para ligar a uma Instância Gerida Azure SQL](connect-vm-instance-configure.md) e [Quickstart: Configure uma ligação ponto-a-local a um Caso Gerido Azure SQL](point-to-site-p2s-configure.md)a partir do local .
> [!IMPORTANT]
> Não é possível criar ou eliminar uma Instância Gerida utilizando a Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CRIAR BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria uma nova base de dados De Instância Gerida. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTERAR BASE DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica uma base de dados Azure SQL Managed Instance.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Criar e gerir casos geridos

Para criar e gerir Instâncias Geridas, utilize estes pedidos REST API.

| Comando | Descrição |
| --- | --- |
|[Instâncias Geridas - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma Instância Gerida.|
|[Instâncias Geridas - Eliminar](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Elimina uma instância gerida.|
|[Instâncias Geridas - Obter](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém uma instância gerida.|
|[Instâncias Geridas - Lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Devolve uma lista de Instâncias Geridas numa subscrição.|
|[Instâncias Geridas - Lista por Grupo de Recursos](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Devolve uma lista de Instâncias Geridas num grupo de recursos.|
|[Instâncias Geridas - Atualização](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza uma Instância Gerida.|

## <a name="next-steps"></a>Próximos passos

- Para saber sobre a migração de uma base de dados do SQL Server para o Azure, consulte [migrate para azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](../database/features-comparison.md).
