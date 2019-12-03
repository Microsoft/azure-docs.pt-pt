---
title: Arquitetura de conectividade
description: Este documento explica a arquitetura de conectividade do SQL do Azure para conexões de banco de dados de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: 6f6c64acf814b39d38138ed0e6a9c6075b693c7d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707989"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure

Este artigo explica o banco de dados SQL do Azure e a arquitetura de conectividade do SQL Data Warehouse, além de como os diferentes componentes funcionam para direcionar o tráfego para sua instância do SQL do Azure. Esses componentes de conectividade funcionam para direcionar o tráfego de rede para o banco de dados SQL do Azure ou SQL Data Warehouse com clientes que se conectam de dentro do Azure e com clientes que se conectam de fora do Azure. Este artigo também fornece exemplos de script para alterar a forma como a conectividade ocorre e as considerações relacionadas à alteração das configurações de conectividade padrão.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade do banco de dados SQL do Azure.

![Visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

As etapas a seguir descrevem como uma conexão é estabelecida com um banco de dados SQL do Azure:

- Os clientes se conectam ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de conexão efetiva, redireciona ou faz o proxy do tráfego para o cluster de banco de dados correto.
- Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados SQL do Azure apropriado.

## <a name="connection-policy"></a>Política de conexão

O banco de dados SQL do Azure dá suporte às três opções a seguir para a configuração de política de conexão de um servidor de banco de dados SQL:

- **Redirecionar (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados, levando à latência reduzida e melhoraram em todo o mundo. Para conexões usarem este modo, os clientes precisam
   - Permitir comunicação de entrada e saída do cliente para todos os endereços IP do Azure na região em portas no intervalo de 11000 11999.  
   - Permita a comunicação de entrada e saída do cliente para endereços IP do gateway do banco de dados SQL do Azure na porta 1433.

- **Proxy:** Nesse modo, todas as conexões são proxy por meio dos gateways de banco de dados SQL do Azure, levando a uma latência maior e reduzidas em todo o mundo. Para conexões usarem esse modo, os clientes precisam permitir a comunicação de entrada e saída do cliente para endereços IP do gateway de banco de dados SQL do Azure na porta 1433.

- **Padrão:** Essa é a política de conexão em vigor em todos os servidores após a criação, a menos que você altere explicitamente a política de conexão para `Proxy` ou `Redirect`. A política padrão é`Redirect` para todas as conexões de cliente originadas dentro do Azure (por exemplo, de uma máquina virtual do Azure) e `Proxy`para todas as conexões de cliente que se originam fora (por exemplo, conexões de sua estação de trabalho local).

 É altamente recomendável a `Redirect` política de conexão sobre a política de conexão de `Proxy` para a menor latência e taxa de transferência mais alta. No entanto, será necessário atender aos requisitos adicionais para permitir o tráfego de rede, conforme descrito acima. Se o cliente for uma máquina virtual do Azure, você poderá fazer isso usando NSG (grupos de segurança de rede) com [marcas de serviço](../virtual-network/security-overview.md#service-tags). Se o cliente estiver se conectando de uma estação de trabalho local, talvez seja necessário trabalhar com o administrador de rede para permitir o tráfego de rede por meio do firewall corporativo.

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure, suas conexões têm uma política de conexão de `Redirect` por padrão. Uma política de `Redirect` significa que, depois que a sessão TCP for estabelecida com o banco de dados SQL do Azure, a sessão do cliente será redirecionada para o cluster de banco de dados correto com uma alteração no IP virtual de destino do gateway do banco de dados SQL do Azure para o do cluster. Depois disso, todos os pacotes subsequentes fluem diretamente para o cluster, ignorando o gateway do banco de dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![Visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, suas conexões terão uma política de conexão de `Proxy` por padrão. Uma política de `Proxy` significa que a sessão TCP é estabelecida por meio do gateway do banco de dados SQL do Azure e de todos os pacotes subsequentes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![Visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Além disso, abra as portas 14000-14999 para habilitar a [conexão com o DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP do gateway da Base de Dados SQL do Azure

A tabela a seguir lista os endereços IP dos gateways por região. Para se conectar a um banco de dados SQL do Azure, você precisa permitir que o tráfego de rede & de **todos os** gateways para a região.

Os detalhes de como o tráfego deve ser migrado para novos gateways em regiões específicas estão no seguinte artigo: [migração de tráfego do banco de dados SQL do Azure para gateways mais recentes](sql-database-gateway-migration.md)


| Nome da Região          | Endereços IP do gateway |
| --- | --- |
| Austrália Central    | 20.36.105.0 |
| Central2 da Austrália   | 20.36.113.0 |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251 |
| Sul do Brasil         | 104.41.11.5, 191.233.200.14 |
| Canadá Central       | 40.85.224.249      |
| Leste do Canadá          | 40.86.226.166      |
| Centro dos E.U.A.           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Norte da China           | 139.219.130.35     |
| Leste da China 2         | 40.73.82.1         |
| China Norte          | 139.219.15.17      |
| Norte da China 2        | 40.73.50.0         |
| Este Asiático            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| Este dos E.U.A.              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| Este dos E.U.A. 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| França Central       | 40.79.137.0, 40.79.129.1 |
| Nordeste da Alemanha      | 51.4.144.100       |
| Leste Norte da Alemanha   | 51.5.144.179       |
| Índia Central        | 104.211.96.159     |
| Índia do Sul          | 104.211.224.146    |
| Oeste da Índia           | 104.211.160.80     |
| Este do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Oeste do Japão           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Coreia do Sul Central        | 52.231.32.42       |
| Sul da Coreia do Sul          | 52.231.200.86      |
| E.U.A. Centro-Norte     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa do Norte         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Norte da África do Sul   | 102.133.152.0      |
| Oeste da África do Sul    | 102.133.24.0       |
| E.U.A. Centro-Sul     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Sudeste Asiático      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| E.A.U. Central          | 20.37.72.64        |
| Norte dos E.A.U.            | 65.52.248.0        |
| Sul do Reino Unido             | 51.140.184.11      |
| Oeste do Reino Unido              | 51.141.8.11        |
| E.U.A. Centro-Oeste      | 13.78.145.25       |
| Europa Ocidental          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| Oeste dos E.U.A.              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| E.U.A. Oeste 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Alterar política de conexão do banco de dados SQL do Azure

Para alterar a política de conexão do banco de dados SQL do Azure para um servidor de banco de dados SQL do Azure, use o comando [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) .

- Se sua política de conexão for definida como `Proxy`, todos os pacotes de rede fluem por meio do gateway do banco de dados SQL do Azure. Para essa configuração, você precisa permitir a saída somente para o IP do gateway do banco de dados SQL do Azure. O uso de uma configuração de `Proxy` tem mais latência do que uma configuração de `Redirect`.
- Se sua política de conexão estiver definindo `Redirect`, todos os pacotes de rede fluem diretamente para o cluster de banco de dados. Para essa configuração, você precisa permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as configurações de conexão por meio do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como alterar a política de conexão.

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

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script para alterar as configurações de conexão via CLI do Azure

> [!IMPORTANT]
> Esse script requer o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

> [!IMPORTANT]
> Esse script requer o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script da CLI a seguir mostra como alterar a política de conexão em um shell bash.

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI do Azure de um prompt de comando do Windows

> [!IMPORTANT]
> Esse script requer o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script da CLI a seguir mostra como alterar a política de conexão de um prompt de comando do Windows (com CLI do Azure instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como alterar a política de conexão do banco de dados SQL do Azure para um servidor de banco de dados SQL do Azure, consulte [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter informações sobre o comportamento de conexão do banco de dados SQL do Azure para clientes que usam o ADO.NET 4,5 ou uma versão posterior, consulte [portas além de 1433 para ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações gerais sobre o desenvolvimento de aplicativos, consulte [visão geral do desenvolvimento de aplicativos do banco de dados SQL](sql-database-develop-overview.md).
