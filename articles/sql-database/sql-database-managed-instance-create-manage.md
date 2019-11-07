---
title: Referência da API de gerenciamento para Instância Gerenciada do Banco de Dados SQL do Azure
description: Saiba mais sobre como criar e gerenciar instâncias gerenciadas do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: bdc17dd11269bafa271f69ec001f8dcad484b7b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688184"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Referência de API gerenciada para instâncias gerenciadas do banco de dados SQL do Azure

Você pode criar e gerenciar instâncias gerenciadas do banco de dados SQL do Azure usando o portal do Azure, o PowerShell, o CLI do Azure, a API REST e o Transact-SQL. Neste artigo, você pode encontrar uma visão geral das funções e da API que você pode usar para criar e configurar Instância Gerenciada.

## <a name="azure-portal-create-a-managed-instance"></a>Portal do Azure: criar uma instância gerenciada

Para obter um guia de início rápido mostrando como criar um Instância Gerenciada do Banco de Dados SQL do Azure, consulte [início rápido: criar um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: criar e gerenciar instâncias gerenciadas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar instâncias gerenciadas com Azure PowerShell, use os cmdlets do PowerShell a seguir. Se você precisar instalar ou atualizar o PowerShell, consulte [instalar Azure PowerShell Module](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [script de início rápido: criar Azure SQL instância gerenciada usando a biblioteca do PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria um Instância Gerenciada do Banco de Dados SQL do Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retorna informações sobre o Azure SQL Instância Gerenciada|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para um Instância Gerenciada do Banco de Dados SQL do Azure|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove uma instância de banco de dados gerenciado do SQL do Azure|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria um banco de dados Instância Gerenciada do Banco de Dados SQL do Azure|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Retorna informações sobre o banco de dados do SQL Instância Gerenciada do Azure|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove um banco de dados de instância de banco de dados gerenciado do Azure SQL|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura um banco de dados de instância de banco de dados gerenciado do Azure SQL|

## <a name="azure-cli-create-and-manage-managed-instances"></a>CLI do Azure: criar e gerenciar instâncias gerenciadas

Para criar e gerenciar instâncias gerenciadas com o [CLI do Azure](/cli/azure), use os comandos [CLI do Azure SQL instância gerenciada](/cli/azure/sql/mi) a seguir. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para obter um CLI do Azure início rápido, consulte [trabalhando com SQL instância gerenciada usando CLI do Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria um Instância Gerenciada|
|[AZ SQL lista de mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Lista as instâncias gerenciadas disponíveis|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obter os detalhes de um Instância Gerenciada|
|[atualização do AZ SQL mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza um Instância Gerenciada|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove um Instância Gerenciada|
|[AZ SQL MidB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria um banco de dados gerenciado|
|[AZ SQL MidB List](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Lista os bancos de dados gerenciados disponíveis|
|[AZ SQL MidB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaurar um banco de dados gerenciado|
|[AZ SQL MidB Delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove um banco de dados gerenciado|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: criar e gerenciar bancos de dados de instância

Para criar e gerenciar o banco de dados de instância após a criação do Instância Gerenciada, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor de banco de dados SQL do Azure e passar comandos TRANSACT-SQL.

> [!TIP]
> Para os guias de início rápido mostrando que você precisa configurar e se conectar a um Instância Gerenciada usando SQL Server Management Studio no Microsoft Windows, consulte [início rápido: configurar a VM do Azure para se conectar a um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-configure-vm.md) e [início rápido: Configure uma conexão ponto a site com um Instância Gerenciada do Banco de Dados SQL do Azure local](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Você não pode criar ou excluir um Instância Gerenciada usando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria um novo banco de dados Instância Gerenciada. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica um banco de dados do SQL Instância Gerenciada do Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>API REST: criar e gerenciar instâncias gerenciadas

Para criar e gerenciar instâncias gerenciadas, use essas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Instâncias gerenciadas – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza um Instância Gerenciada.|
|[Instâncias gerenciadas-excluir](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Exclui um Instância Gerenciada.|
|[Instâncias gerenciadas-obter](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém um Instância Gerenciada.|
|[Instâncias gerenciadas-lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retorna uma lista de instâncias gerenciadas em uma assinatura.|
|[Instâncias gerenciadas-listar por grupo de recursos](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retorna uma lista de instâncias gerenciadas em um grupo de recursos.|
|[Instâncias gerenciadas-atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza um Instância Gerenciada.|

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como migrar um banco de dados SQL Server para o Azure, consulte [migrar para o banco de dados SQL do Azure](sql-database-single-database-migrate.md)
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).
