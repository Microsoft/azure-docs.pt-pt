---
title: Arquitetura de conectividade do armazém de dados SQL e de base de dados SQL do Azure | Documentos da Microsoft
description: Este documento explica a arquitetura de conectividade de SQL do Azure para ligações da base de dados a partir de dentro do Azure ou a partir de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 8441e64981b7157e91a56124a08c0aa02a9b1db0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537938"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure

Este artigo explica a base de dados do Azure SQL e SQL Data Warehouse conectividade arquitetura, bem como a forma como os diferentes componentes de função para direcionar o tráfego à sua instância do SQL do Azure. Estes função de componentes de conectividade para direcionar o tráfego de rede para a base de dados do Azure SQL ou SQL Data Warehouse com os clientes ligar a partir de dentro do Azure e com os clientes ligar a partir de fora do Azure. Este artigo também fornece exemplos de script para alterar a forma como ocorre a conectividade e as considerações relacionadas com a alteração das definições de conectividade do padrão.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama seguinte fornece uma visão geral da arquitetura de conectividade SQL Database do Azure.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Os passos seguintes descrevem como é estabelecida uma ligação para uma base de dados SQL do Azure:

- Os clientes ligam-se para o gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de ligação em vigor, redirecionamentos ou proxies o tráfego para o cluster de banco de dados correta.
- No banco de dados o tráfego de cluster é encaminhado para a base de dados SQL do Azure adequada.

## <a name="connection-policy"></a>Política de ligação

Base de dados SQL do Azure suporta as seguintes três opções para a definição de política de ligação de um servidor de base de dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente para o nó que aloja a base de dados. Para ativar a conectividade, os clientes têm de permitir regras de firewall de saída para todos os endereços de IP do Azure na região a utilizar grupos de segurança de rede (NSG) com [etiquetas de serviço](../virtual-network/security-overview.md#service-tags)) para portas 11000-11999, não apenas o IP do gateway de base de dados do Azure SQL endereços na porta 1433. Porque pacotes ir diretamente para a base de dados, latência e débito melhoraram o desempenho.
- **Proxy:** Neste modo, todas as ligações são transmitidas por proxy através de gateways de base de dados do Azure SQL. Para ativar a conectividade, o cliente tem de ter regras de firewall de saída que permitam apenas o gateway da base de dados do Azure SQL endereços IP (normalmente, dois endereços IP por região). Escolher este modo pode resultar numa maior latência e débito mais baixo, dependendo da natureza da carga de trabalho. Recomendamos vivamente o `Redirect` política de ligação ao longo o `Proxy` política de ligação para a menor latência e o débito mais elevado.
- **predefinição:** Esta é a ligação política em vigor em todos os servidores após a criação, a menos que alterar explicitamente a diretiva de conexão para um `Proxy` ou `Redirect`. A política em vigor depende se conexões têm origem no Azure (`Redirect`) ou fora do Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Conectividade a partir do Azure

Se estiver a ligar a partir de dentro do Azure suas conexões têm uma política de ligação de `Redirect` por predefinição. Uma política de `Redirect` significa que depois da sessão TCP é estabelecida para a base de dados SQL do Azure, a sessão de cliente é redirecionada para o cluster de banco de dados correta com uma alteração para o IP virtual de destino do que o gateway de base de dados do Azure SQL das cluster. Por esse motivo, todos os pacotes subsequentes fluxo diretamente para o cluster, ignorando o gateway de base de dados do Azure SQL. O diagrama seguinte ilustra esse fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade a partir de fora do Azure

Se estiver a ligar de fora do Azure, as suas ligações têm uma política de ligação de `Proxy` por predefinição. Uma política de `Proxy` significa que a sessão TCP é estabelecida através do gateway da base de dados do Azure SQL e todos os pacotes subsequentes fluir através do gateway. O diagrama seguinte ilustra esse fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços de IP do gateway da base de dados SQL do Azure

A tabela abaixo lista os endereços IP dos Gateways por região. Para ligar a uma base de dados do SQL do Azure, tem de permitir tráfego de rede a & partir **todos os** Gateways para a região.

Daqui em diante, vamos adicionar mais Gateways em cada região e extinguir os Gateways na coluna de endereço IP do Gateway de fora de utilização da tabela abaixo. Obter mais detalhes sobre como desativar o processo tendo em conta no seguinte artigo: [Migração de tráfego de base de dados SQL do Azure para Gateways mais recente](sql-database-gateway-migration.md)


| Nome da Região          | Endereço IP do gateway | Gateway desativado </br> Endereço IP| Notas sobre desativar | 
| --- | --- | --- | --- |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1 | | |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251 | | |
| Sul do Brasil         | 104.41.11.5        |                 | |
| Canadá Central       | 40.85.224.249      |                 | |
| Leste do Canadá          | 40.86.226.166      |                 | |
| EUA Central           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Sem ligações após 1 de Setembro de 2019 |
| Leste da China 1         | 139.219.130.35     |                 | |
| Leste da China 2         | 40.73.82.1         |                 | |
| Norte da China 1        | 139.219.15.17      |                 | |
| Norte da China 2        | 40.73.50.0         |                 | |
| Ásia Oriental            | 191.234.2.139, 52.175.33.150 |       | |
| E.U.A. Leste 1            | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Sem ligações após 1 de Setembro de 2019 |
| EUA Leste 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Sem ligações após 1 de Setembro de 2019 |
| França Central       | 40.79.137.0, 40.79.129.1 |           | |
| Alemanha Central      | 51.4.144.100       |                 | |
| Centro-Norte leste da Alemanha   | 51.5.144.179       |                 | |
| Índia Central        | 104.211.96.159     |                 | |
| Índia do Sul          | 104.211.224.146    |                 | |
| Oeste da Índia           | 104.211.160.80     |                 | |
| Leste do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Sem ligações após 1 de Setembro de 2019 |
| Oeste do Japão           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Sem ligações após 1 de Setembro de 2019 |
| Coreia do Sul Central        | 52.231.32.42       |                 | |
| Coreia do Sul          | 52.231.200.86      |                 | |
| EUA Centro-Norte     | 23.96.178.199      | 23.98.55.75     | Sem ligações após 1 de Setembro de 2019 |
| Europa do Norte         | 40.113.93.91       | 191.235.193.75  | Sem ligações após 1 de Setembro de 2019 |
| EUA Centro-Sul     | 13.66.62.124       | 23.98.162.75    | Sem ligações após 1 de Setembro de 2019 |
| Sudeste Asiático      | 104.43.15.0        | 23.100.117.95   | Sem ligações após 1 de Setembro de 2019 |
| Reino Unido Sul             | 51.140.184.11      |                 | |
| Reino Unido Oeste              | 51.141.8.11        |                 | |
| EUA Centro-Oeste      | 13.78.145.25       |                 | |
| Europa Ocidental          | 191.237.232.75, 40.68.37.158 |       | |
| E.U.A. oeste 1            | 23.99.34.75, 104.42.238.205 |        | |
| EUA Oeste 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de ligação de base de dados do Azure SQL

Para alterar a política de ligação de base de dados do Azure SQL para um servidor de base de dados do Azure SQL, utilize o [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) comando.

- Se a política de ligação é definida para `Proxy`, todo o fluxo de pacotes através do gateway da base de dados do Azure SQL de rede. Para esta definição, terá de permitir a saída para apenas o IP de gateway do Azure SQL Database. Usar uma definição de `Proxy` tem uma latência mais que uma definição de `Redirect`.
- Se a sua política de ligação é a configuração `Redirect`, todo o fluxo de pacotes diretamente para o cluster de base de dados de rede. Para esta definição, terá de permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as definições de ligação através do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O seguinte script requer os [módulo do Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell seguinte mostra como alterar a diretiva de conexão.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script para alterar as definições de ligação através da CLI do Azure

> [!IMPORTANT]
> Este script requer os [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure numa bash shell

> [!IMPORTANT]
> Este script requer os [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script CLI seguinte mostra como alterar a diretiva de conexão num shell de bash.

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI do Azure num prompt de comando do Windows

> [!IMPORTANT]
> Este script requer os [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script CLI seguinte mostra como alterar a diretiva de conexão num prompt de comando do Windows (com a CLI do Azure instalada).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como alterar a política de ligação de base de dados do Azure SQL para um servidor de base de dados do Azure SQL, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter informações sobre o comportamento de ligação de base de dados do Azure SQL para clientes que utilizam o ADO.NET 4.5 ou posterior, consulte [portas para além do 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para informações de descrição geral de desenvolvimento de aplicações gerais, veja [descrição geral do desenvolvimento de aplicações de base de dados do SQL](sql-database-develop-overview.md).