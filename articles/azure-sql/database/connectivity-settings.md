---
title: Definições de conectividade para Base de Dados EArmazém de Dados Azure SQL
description: Este documento explica a escolha da versão TLS e a definição proxy vs. redirecionamento para a Base de Dados Azure SQL e Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 7b8fc2145c8b1c9da67f0ef44397b0e765b03de2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046608"
---
# <a name="azure-sql-connectivity-settings"></a>Definições de Conectividade do SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo introduz configurações que controlam a conectividade do servidor para a Base de Dados Azure SQL e para a Azure Synapse Analytics. Estas definições aplicam-se a **todas as** bases de dados SQL e Azure Synapse associadas ao servidor.

> [!IMPORTANT]
> Este artigo *não* se aplica à **Instância Gerida Azure SQL**

As definições de conectividade são acessíveis a partir do ecrã **firewalls e redes virtuais,** como mostra a seguinte imagem:

 ![Screenshot das definições de conectividade][1]

> [!NOTE]
> Uma vez aplicadas estas definições, **estas efetuam efeitos imediatamente** e podem resultar em perdas de ligação para os seus clientes se não cumprirem os requisitos para cada configuração.

## <a name="deny-public-network-access"></a>Negar o acesso à rede pública

No portal Azure, quando a definição de acesso à **rede pública Deny** está definida para **Sim**, apenas são permitidas ligações através de pontos finais privados. Quando esta definição está definida para **Não,** os clientes podem ligar-se utilizando o ponto final privado ou público.

Os clientes podem ligar-se à Base de Dados SQL utilizando pontos finais públicos (regras de firewall baseadas em IP, regras de firewall baseadas em VNET) ou pontos finais privados (utilizando private link) conforme descrito na [visão geral](network-access-controls-overview.md)do acesso à rede . 

Quando a definição de acesso à **rede pública Deny** é definida para **Sim**, apenas são permitidas ligações através de pontos finais privados e todas as ligações através de pontos finais públicos são negadas com uma mensagem de erro semelhante a:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública via PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como e a propriedade de Acesso à `Get` `Set` Rede **Pública** ao nível do servidor:

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

## <a name="minimal-tls-version"></a>Versão Mínima de TLS 

A definição de versão De forma ção de Segurança da Camada de Transporte Mínima [(TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite aos clientes controlar a versão do TLS utilizada pela sua Base de Dados Azure SQL.

Atualmente apoiamos TLS 1.0, 1.1 e 1.2. A definição de uma versão TLS Mínima garante que as versões TLS subsequentes e mais recentes são suportadas. Por exemplo, por exemplo, escolher uma versão TLS superior a 1.1. significa que apenas são aceites ligações com TLS 1.1 e 1.2 e o TLS 1.0 é rejeitado. Após os testes para confirmar que as suas aplicações suportam o mesmo, recomendamos a definição de versão TLS mínima para 1.2, uma vez que inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais alta do TLS suportada na Base de Dados Azure SQL.

Para clientes com aplicações que dependam de versões mais antigas do TLS, recomendamos a definição da versão TLS mínima de acordo com os requisitos das suas aplicações. Para clientes que dependem de aplicações para se conectarem usando uma ligação não encriptada, recomendamos que não estabeleça qualquer versão TLS mínima. 

Para mais informações, consulte [as considerações de TLS para](connect-query-content-reference-guide.md#tls-considerations-for-sql-database-connectivity)a conectividade da Base de Dados SQL .

Depois de definir a versão TLS Mínima, as tentativas de login dos clientes que estão a utilizar uma versão TLS inferior à versão TLS Mínima do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir versão TLS mínima via PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade versão `Set` **TLS Mínima** ao nível lógico do servidor:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir versão TLS mínima via Azure CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [O ClI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de festa

O seguinte script CLI mostra como alterar a definição de **versão TLS mínima** numa concha de bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Alterar a política de ligação

A política de [ligação](connectivity-architecture.md#connection-policy) determina como os clientes se ligam à Base de Dados Azure SQL.


## <a name="change-connection-policy-via-powershell"></a>Alterar a política de ligação via PowerShell

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

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral de como a conectividade funciona na Base de Dados Azure SQL, consulte a [Conectividade Arquitetura](connectivity-architecture.md)
- Para obter informações sobre como alterar a política de ligação de um servidor, consulte a [política de conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
