---
title: Definições de conectividade para Azure SQL Database e Data Warehouse
description: Este documento explica a escolha da versão de Segurança da Camada de Transporte (TLS) e a definição de Proxy vs. Redirecionamento para Azure SQL Database e Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: eecd4220cdda471807e4b84261d7f76c31b9ba70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92672339"
---
# <a name="azure-sql-connectivity-settings"></a>Definições de Conectividade do SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo introduz configurações que controlam a conectividade com o servidor para Azure SQL Database e Azure Synapse Analytics. Estas definições aplicam-se a **todas as** bases de dados SQL Database e Azure Synapse associadas ao servidor.

> [!IMPORTANT]
> Este artigo *não* se aplica a **Azure SQL Managed Instance** .

As definições de conectividade estão acessíveis a partir do ecrã **de Firewalls e redes virtuais,** como mostrado na imagem seguinte:

 ![Screenshot das definições de conectividade][1]

> [!NOTE]
> Uma vez aplicadas estas definições, **elas fazem efeito imediatamente** e podem resultar em perda de ligação para os seus clientes se não cumprirem os requisitos para cada configuração.

## <a name="deny-public-network-access"></a>Negar acesso à rede pública

Quando a definição **de acesso à rede pública deny** está definida para **Sim,** apenas são permitidas ligações através de pontos finais privados. Quando esta definição é definida como **Nº** (padrão), os clientes podem ligar-se utilizando pontos finais públicos (regras de firewall baseadas em IP, regras de firewall baseadas em VNET) ou pontos finais privados (usando o Link Privado) conforme descrito na [visão geral](network-access-controls-overview.md)de acesso à rede . 

 ![Screenshot da conectividade com o acesso à rede pública negar o acesso à rede pública][2]

Qualquer tentativa de definir a definição **de acesso à rede pública** de Deny para **Sim** sem quaisquer pontos finais privados existentes no servidor lógico falhará com uma mensagem de erro semelhante a:  

> [!NOTE]
> Para definir as regras de firewall de rede virtual num servidor lógico que já está configurado com pontos finais privados, defina **o acesso da rede pública** ao **Nº** .

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Quando a definição de acesso à rede pública de **Negação** é definida como **Sim,** apenas são permitidas ligações através de pontos finais privados e todas as ligações através de pontos finais públicos são negadas com uma mensagem de erro semelhante a:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando a definição de acesso à rede pública de **Negação** estiver definida como **Sim,** qualquer tentativa de adicionar ou atualizar as regras de firewall será negada com uma mensagem de erro semelhante a:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar acesso à rede pública via PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade de Acesso à Rede `Set` **Pública** ao nível do servidor:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Alterar acesso à rede pública via CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash

O seguinte script CLI mostra como alterar o Acesso à **Rede Pública** numa concha de bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Versão minimalista de TLS 

A definição de versão minimal [transport layer security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes controlem a versão do TLS utilizada pela sua Base de Dados Azure SQL.

Neste momento, apoiamos os TLS 1.0, 1.1 e 1.2. A definição de uma versão TLS mínima garante que as versões TLS mais recentes sejam suportadas. Por exemplo, escolher uma versão TLS superior a 1.1. significa que apenas são aceites ligações com os TLS 1.1 e 1.2 e rejeitadas as TLS 1.0. Após testes para confirmar que as suas aplicações o suportam, recomendamos definir a versão Minimal TLS para 1.2, uma vez que inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais alta do TLS suportada na Base de Dados Azure SQL.

> [!IMPORTANT]
> O padrão para a versão Minimal TLS é permitir todas as versões. No entanto, uma vez que executa uma versão de TLS não é possível reverter para o padrão.

Para clientes com aplicações que dependem de versões mais antigas do TLS, recomendamos definir a Versão Minimal TLS de acordo com os requisitos das suas aplicações. Para os clientes que dependem de aplicações para se conectarem utilizando uma ligação não encriptada, recomendamos não definir qualquer versão Minimal TLS.

Para obter mais informações, consulte [considerações de TLS para a conectividade sql Database](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão Minimal TLS, as tentativas de login de clientes que estão a utilizar uma versão TLS inferior à versão Minimal TLS do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir a versão mínima TLS via PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O seguinte script PowerShell mostra como `Get` e a propriedade de versão Minimal `Set` **TLS** ao nível do servidor lógico:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir versão mínima de TLS via Azure CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash

O seguinte script CLI mostra como alterar a definição **de versão Minimal TLS** numa casca de bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Alterar a política de ligação

[A política de ligação](connectivity-architecture.md#connection-policy) determina como os clientes se ligam à Base de Dados Azure SQL.


## <a name="change-connection-policy-via-powershell"></a>Alterar a política de ligação via PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

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

## <a name="change-connection-policy-via-azure-cli"></a>Alterar a política de conexão via Azure CLI

> [!IMPORTANT]
> Todos os scripts desta secção requerem [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash

O seguinte script CLI mostra como alterar a política de conexão numa casca de bash:

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

O seguinte script CLI mostra como alterar a política de ligação a partir de um pedido de comando do Windows (com O Azure CLI instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral de como a conectividade funciona na Base de Dados Azure SQL, consulte a [Arquitetura de Conectividade](connectivity-architecture.md)
- Para obter informações sobre como alterar a política de ligação de um servidor, consulte [a política de conn](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png