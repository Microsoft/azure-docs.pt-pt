---
title: Definições de conectividade para Base de Dados EArmazém de Dados Azure SQL
description: Este documento explica a escolha da versão TLS e a definição proxy vs. redirecionamento para Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366092"
---
# <a name="azure-sql-connectivity-settings"></a>Definições de conectividade Azure SQL
> [!NOTE]
> Este artigo aplica-se ao servidor Azure SQL e tanto às bases de dados SQL como ao SQL Data Warehouse que são criados no servidor Azure SQL. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo *não* se aplica à Instância Gerida pela Base de **Dados Azure SQL**

Este artigo introduz configurações que controlam a conectividade com a Base de Dados Azure SQL ao nível do servidor. Estas definições aplicam-se a **todas as** bases de dados sQL e sQL Data Warehouse associadas ao servidor.

> [!NOTE]
> Uma vez aplicadas estas definições, **estas efetuam efeitos imediatamente** e podem resultar em perdas de ligação para os seus clientes se não cumprirem os requisitos para cada configuração.

As definições de conectividade são acessíveis a partir das Firewalls e da lâmina de **redes virtuais,** como mostra a imagem abaixo:

 ![Screenshot das definições de conectividade][1]


## <a name="deny-public-network-access"></a>Negar o acesso à rede pública
No portal Azure, quando a definição de acesso à **rede pública Deny** está definida para **Sim**, apenas são permitidas ligações através de pontos finais privados. Quando esta definição está definida para **Não,** os clientes podem ligar-se utilizando o ponto final privado ou público.

Depois de definir o acesso da **rede pública Denegação** a **Sim,** as tentativas de login dos clientes que usam o ponto final público falharão com o seguinte erro:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell `Get` `Set` mostra como e a propriedade de Acesso à **Rede Pública** ao nível lógico do servidor:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Alterar o acesso à rede pública via CLI
> [!IMPORTANT]
> Todos os scripts desta secção requerem [O ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de festa
O seguinte guião cli mostra como mudar o Acesso da **Rede Pública** numa concha de bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Política de ligação
A política de [ligação](sql-database-connectivity-architecture.md#connection-policy) determina como os clientes se ligam ao Servidor SQL Azure. 

## <a name="change-connection-policy-via-powershell"></a>Alterar a política de ligação via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como alterar a política de ligação usando powerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Alterar a política de ligação via Azure CLI
> [!IMPORTANT]
> Todos os scripts desta secção requerem [O ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de festa
O seguinte guião cli mostra como alterar a política de conexão numa concha de bash: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI de um pedido de comando do Windows
O seguinte script CLI mostra como alterar a política de ligação a partir de um pedido de comando Windows (com o Azure CLI instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos seguintes
- Para uma visão geral de como a conectividade funciona na Base de Dados Azure SQL, consulte a [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)
- Para obter informações sobre como alterar a política de ligação da Base de Dados Azure SQL para um servidor de base de dados Azure SQL, consulte a [política de conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
