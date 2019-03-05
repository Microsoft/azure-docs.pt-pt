---
title: Direcionar o tráfego do Azure para a base de dados do Azure SQL e SQL Data Warehouse | Documentos da Microsoft
description: Este documento explica a base de dados do Azure SQL e SQL Data Warehouse conectividade arquitetura de dentro do Azure ou a partir de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: dde54894867811a34340f6dd4d0236b6b54e8144
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307722"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure

Este artigo explica a base de dados do Azure SQL e SQL Data Warehouse conectividade arquitetura, bem como a forma como os diferentes componentes de função para direcionar o tráfego à sua instância do SQL do Azure. Estes função de componentes de conectividade para direcionar o tráfego de rede para a base de dados do Azure SQL ou SQL Data Warehouse com os clientes ligar a partir de dentro do Azure e com os clientes ligar a partir de fora do Azure. Este artigo também fornece exemplos de script para alterar a forma como ocorre a conectividade e as considerações relacionadas com a alteração das definições de conectividade do padrão.

> [!IMPORTANT]
> **[Alteração futura] Para ligações de ponto final de serviço para servidores SQL do Azure, um `Default` comportamento de conectividade é alterado para `Redirect`.**
> Os clientes são aconselhados a criar novos servidores e o conjunto já existentes com o tipo de ligação explicitamente definida para redirecionamento (preferível) ou Proxy, dependendo da sua arquitetura de conectividade.
>
> Para impedir a conectividade através de um ponto de extremidade de serviço do usuário em ambientes existentes, como resultado desta alteração, podemos usar fazer de telemetria o seguinte:
>
> - Para servidores que detectamos que foram acedidos através de pontos finais de serviço antes da alteração, vamos mudar o tipo de ligação para `Proxy`.
> - Para todos os outros servidores, podemos mudar a ligação irá mudar para o tipo `Redirect`.
>
> Os utilizadores do ponto final de serviço ainda poderão ser afetados nos seguintes cenários:
>
> - Aplicativo se conecta a um servidor existente com pouca frequência para que nossa telemetria não captura as informações sobre esses aplicativos
> - Lógica de implementação automatizada cria um servidor de base de dados SQL, partindo do princípio de que é o comportamento predefinido para ligações de ponto final de serviço `Proxy`
>
> Se não foi possível estabelecer ligações de ponto final de serviço para o servidor SQL do Azure e são suspecting o que são afetados por esta alteração, verifique se o tipo de ligação está explicitamente definido como `Redirect`. Se for este o caso, terá de abrir as regras de firewall VM e grupos de segurança de rede (NSG) para todos os endereços de IP do Azure na região que pertençam a Sql [etiqueta de serviço](../virtual-network/security-overview.md#service-tags) para portas 11000 12000. Se não for uma opção para, alternar servidor explicitamente como `Proxy`.
> [!NOTE]
> Este tópico aplica-se para os servidores de base de dados do Azure SQL que alojam bases de dados individuais e conjuntos elásticos e bases de dados do SQL Data Warehouse. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama seguinte fornece uma visão geral da arquitetura de conectividade SQL Database do Azure.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Os passos seguintes descrevem como é estabelecida uma ligação para uma base de dados SQL do Azure:

- Os clientes ligam-se para o gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de ligação em vigor, redirecionamentos ou proxies o tráfego para o cluster de banco de dados correta.
- No banco de dados o tráfego de cluster é encaminhado para a base de dados SQL do Azure adequada.

## <a name="connection-policy"></a>Política de ligação

Base de dados SQL do Azure suporta as seguintes três opções para a definição de política de ligação de um servidor de base de dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente para o nó que aloja a base de dados. Para ativar a conectividade, os clientes têm de permitir regras de firewall de saída para todos os endereços de IP do Azure na região a utilizar grupos de segurança de rede (NSG) com [etiquetas de serviço](../virtual-network/security-overview.md#service-tags)) para portas 11000-12000, não apenas o IP do gateway de base de dados do Azure SQL endereços na porta 1433. Porque pacotes ir diretamente para a base de dados, latência e débito melhoraram o desempenho.
- **Proxy:** Neste modo, todas as ligações são transmitidas por proxy através de gateways de base de dados do Azure SQL. Para ativar a conectividade, o cliente tem de ter regras de firewall de saída que permitam apenas o gateway da base de dados do Azure SQL endereços IP (normalmente, dois endereços IP por região). Escolher este modo pode resultar numa maior latência e débito mais baixo, dependendo da natureza da carga de trabalho. Recomendamos vivamente o `Redirect` política de ligação ao longo o `Proxy` política de ligação para a menor latência e o débito mais elevado.
- **predefinição:** Esta é a ligação política em vigor em todos os servidores após a criação, a menos que alterar explicitamente a diretiva de conexão para um `Proxy` ou `Redirect`. A política em vigor depende se conexões têm origem no Azure (`Redirect`) ou fora do Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Conectividade a partir do Azure

Se estiver a ligar a partir de dentro do Azure suas conexões têm uma política de ligação de `Redirect` por predefinição. Uma política de `Redirect` significa que depois da sessão TCP é estabelecida para a base de dados SQL do Azure, a sessão de cliente é redirecionada para o cluster de banco de dados correta com uma alteração para o IP virtual de destino do que o gateway de base de dados do Azure SQL das cluster. Por esse motivo, todos os pacotes subsequentes fluxo diretamente para o cluster, ignorando o gateway de base de dados do Azure SQL. O diagrama seguinte ilustra esse fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade a partir de fora do Azure

Se estiver a ligar de fora do Azure, as suas ligações têm uma política de ligação de `Proxy` por predefinição. Uma política de `Proxy` significa que a sessão TCP é estabelecida através do gateway da base de dados do Azure SQL e todos os pacotes subsequentes fluir através do gateway. O diagrama seguinte ilustra esse fluxo de tráfego.

![Descrição geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços de IP do gateway da base de dados SQL do Azure

Para ligar a uma base de dados SQL do Azure a partir de recursos no local, terá de permitir tráfego de rede de saída para o gateway de base de dados do Azure SQL para a sua região do Azure. As suas ligações multiplicar apenas através do gateway ao ligar-se no `Proxy` modo, o que é o padrão quando ligar a partir de recursos no local.

A tabela seguinte lista os IPs primário e secundário do gateway para todas as regiões de dados SQL Database do Azure. Para algumas regiões, existem dois endereços IP. Nestas regiões, o endereço IP principal é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de ativação pós-falha. O endereço de ativação pós-falha é o endereço para o qual estamos pode ser movida seu servidor para manter a alta disponibilidade do serviço. Para estas regiões, recomendamos que permite que saída para os endereços IP. O segundo endereço IP pertence à Microsoft e não escutar em quaisquer serviços até que seja ativado pela base de dados do Azure SQL para aceitar ligações.

| Nome da Região | Endereço IP primário | Endereço IP secundário |
| --- | --- |--- |
| Leste da Austrália | 13.75.149.87 | 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| EUA Central | 23.99.160.139 | 13.67.215.62 |
| Leste da China 1 | 139.219.130.35 | |
| Leste da China 2 | 40.73.82.1 | |
| Norte da China 1 | 139.219.15.17 | |
| Norte da China 2 | 40.73.50.0 | |
| Ásia Oriental | 191.234.2.139 | 52.175.33.150 |
| E.U.A. Leste 1 | 191.238.6.43 | 40.121.158.30 |
| EUA Leste 2 | 191.239.224.107 | 40.79.84.180 * |
| França Central | 40.79.137.0 | 40.79.129.1 |
| Alemanha Central | 51.4.144.100 | |
| Centro-Norte leste da Alemanha | 51.5.144.179 | |
| Índia Central | 104.211.96.159 | |
| Índia do Sul | 104.211.224.146 | |
| Oeste da Índia | 104.211.160.80 | |
| Leste do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia do Sul Central | 52.231.32.42 | |
| Coreia do Sul | 52.231.200.86 |  |
| EUA Centro-Norte | 23.98.55.75 | 23.96.178.199 |
| Europa do Norte | 191.235.193.75 | 40.113.93.91 |
| EUA Centro-Sul | 23.98.162.75 | 13.66.62.124 |
| Sudeste Asiático | 23.100.117.95 | 104.43.15.0 |
| Reino Unido Sul | 51.140.184.11 | |
| EUA Centro-Oeste | 13.78.145.25 | |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| E.U.A. oeste 1 | 23.99.34.75 | 104.42.238.205 |
| EUA Oeste 2 | 13.66.226.202 | |
||||

\* **NOTA:** *E.U.A. Leste 2* também tem um endereço IP terciário de `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de ligação de base de dados do Azure SQL

Para alterar a política de ligação de base de dados do Azure SQL para um servidor de base de dados do Azure SQL, utilize o [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) comando.

- Se a política de ligação é definida para `Proxy`, todo o fluxo de pacotes através do gateway da base de dados do Azure SQL de rede. Para esta definição, terá de permitir a saída para apenas o IP de gateway do Azure SQL Database. Usar uma definição de `Proxy` tem uma latência mais que uma definição de `Redirect`.
- Se a sua política de ligação é a configuração `Redirect`, todo o fluxo de pacotes diretamente para o cluster de base de dados de rede. Para esta definição, terá de permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as definições de ligação através do PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Este script requer os [módulo do Azure PowerShell](/powershell/azure/install-az-ps).

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
> Este script requer os [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O script CLI seguinte mostra como alterar a diretiva de conexão.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como alterar a política de ligação de base de dados do Azure SQL para um servidor de base de dados do Azure SQL, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter informações sobre o comportamento de ligação de base de dados do Azure SQL para clientes que utilizam o ADO.NET 4.5 ou posterior, consulte [portas para além do 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para informações de descrição geral de desenvolvimento de aplicações gerais, veja [descrição geral do desenvolvimento de aplicações de base de dados do SQL](sql-database-develop-overview.md).
