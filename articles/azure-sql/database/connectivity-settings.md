---
title: Definições de conectividade para Azure SQL Database e Azure Synapse Analytics
description: Este artigo explica a escolha da versão de Segurança da Camada de Transporte (TLS) e as definições de Proxy versus Redirect para Azure SQL Database e Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 0c45a48e6cafa722945400554f2f81916da13775
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627613"
---
# <a name="azure-sql-connectivity-settings"></a>Definições de conectividade Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo introduz configurações que controlam a conectividade com o servidor da Azure SQL Database e [piscina de SQL dedicada (anteriormente SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) no Azure Synapse Analytics. Estas definições aplicam-se a todas as bases de dados SQL Database e à pool de SQL dedicada (anteriormente SQL DW) associadas ao servidor.

> [!IMPORTANT]
> Este artigo não se aplica a Azure SQL Managed Instance.

As definições de conectividade estão acessíveis a partir do ecrã **de Firewalls e redes virtuais,** como mostrado na imagem seguinte:

 ![Screenshot da janela de definições de conectividade.][1]

> [!NOTE]
> Estas definições fazem efeito imediatamente após a sua aplicação. Os seus clientes podem sofrer perdas de conexão se não cumprirem os requisitos para cada configuração.

## <a name="deny-public-network-access"></a>Negar acesso à rede pública

Quando **o acesso à rede pública Deny** é definido para **Sim,** apenas são permitidas ligações através de pontos finais privados. Quando esta definição é **Nº** (padrão), os clientes podem ligar-se utilizando pontos finais públicos (com regras de firewall baseadas em IP ou com regras de firewall baseadas em rede virtual) ou pontos finais privados (utilizando o Azure Private Link), conforme descrito na [visão geral](network-access-controls-overview.md)do acesso à rede .

 ![Diagrama que mostra conectividade quando o acesso à rede pública deny é definido para sim versus quando o acesso à rede pública deny está definido para não.][2]

Qualquer tentativa de definir **o acesso da rede pública** a **"Sim"** sem quaisquer pontos finais privados existentes no servidor lógico falhará com uma mensagem de erro semelhante a:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Para definir as regras de firewall de rede virtual num servidor lógico que já foi configurado com pontos finais privados, defina **o acesso da rede pública** ao **Nº**.

Quando **o acesso à rede pública Deny** é definido para **Sim,** apenas são permitidas ligações através de pontos finais privados. Todas as ligações através de pontos finais públicos serão negadas com uma mensagem de erro semelhante a:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando **o acesso à rede pública deny** estiver definido para **Sim,** qualquer tentativa de adicionar ou atualizar as regras de firewall será negada com uma mensagem de erro semelhante a:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública via PowerShell

> [!IMPORTANT]
> A Azure SQL Database ainda suporta o módulo PowerShell Azure Resource Manager, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade de Acesso à Rede `Set` **Pública** ao nível do servidor:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Alterar o acesso à rede pública via CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem o [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha bash

O seguinte script CLI mostra como alterar a definição de Acesso à **Rede Pública** numa concha bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Versão mínima do TLS 

A definição mínima [de segurança da camada de transporte (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes escolham qual a versão do TLS que a sua base de dados SQL utiliza.

Atualmente, suportamos o TLS 1.0, 1.1 e 1.2. A definição de uma versão mínima do TLS garante que as versões do TLS mais recentes são suportadas. Por exemplo, escolher uma versão TLS 1.1 significa apenas que são aceites ligações com TLS 1.1 e 1.2 e as ligações com tLS 1.0 são rejeitadas. Depois de testar para confirmar que as aplicação o suportam, é recomendável definir a versão mínima do TLS como 1.2. Esta versão inclui correções para vulnerabilidades em versões anteriores e é a versão mais recente do TLS suportada na Base de Dados SQL do Azure.

> [!IMPORTANT]
> O padrão para a versão mínima TLS é permitir todas as versões. Depois de impor uma versão do TLS, não é possível reverter para o padrão.

Para clientes com aplicações que dependem de versões mais antigas do TLS, recomendamos definir a versão mínima do TLS de acordo com os requisitos das aplicações. Para clientes que dependem de aplicações para se ligarem através de uma ligação não encriptada, recomendamos não definir nenhuma versão mínima do TLS.

Para obter mais informações, consulte [considerações de TLS para a conectividade sql Database](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão mínima TLS, as tentativas de login de clientes que estão a utilizar uma versão TLS inferior à versão TLS mínima do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Desa esta versão TLS mínima via PowerShell

> [!IMPORTANT]
> A Azure SQL Database ainda suporta o módulo PowerShell Azure Resource Manager, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade de versão Minimal `Set` **TLS** ao nível do servidor lógico:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Desconfi deixe a versão mínima de TLS através do CLI Azure

> [!IMPORTANT]
> Todos os scripts desta secção requerem o [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha bash

O seguinte script CLI mostra como alterar a definição **de versão Minimal TLS** numa concha Bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Alterar a política de ligação

[A política de ligação](connectivity-architecture.md#connection-policy) determina como os clientes se ligam à Base de Dados Azure SQL.

## <a name="change-the-connection-policy-via-powershell"></a>Alterar a política de ligação via PowerShell

> [!IMPORTANT]
> A Azure SQL Database ainda suporta o módulo PowerShell Azure Resource Manager, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como alterar a política de ligação utilizando o PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Alterar a política de ligação através do CLI Azure

> [!IMPORTANT]
> Todos os scripts desta secção requerem o [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha bash

O seguinte script CLI mostra como alterar a política de conexão numa concha bash:

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

O seguinte script CLI mostra como alterar a política de ligação a partir de um pedido de comando do Windows (com o CLI Azure instalado):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral de como a conectividade funciona na Base de Dados Azure SQL, consulte a [arquitetura conectividade.](connectivity-architecture.md)
- Para obter informações sobre como alterar a política de ligação de um servidor, consulte [a política de conn](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
