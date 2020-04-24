---
title: Arquitetura de Conectividade
description: Este documento explica a arquitetura de conectividade Azure SQL para ligações de base de dados dentro do Azure ou de fora do Azure.
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
ms.date: 03/09/2020
ms.openlocfilehash: b4e7d827536245a22d168c7d9923c2e5b82830b0
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111798"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de Conectividade do SQL do Azure
> [!NOTE]
> Este artigo aplica-se ao servidor Azure SQL e tanto às bases de dados SQL como ao SQL Data Warehouse que são criados no servidor Azure SQL. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo *não* se aplica à instância gerida pela Base de **Dados Azure SQL**. Consulte a [arquitetura de conectividade para uma instância gerida.](sql-database-managed-instance-connectivity-architecture.md)

Este artigo explica a arquitetura de vários componentes que direcionam o tráfego de rede para a Base de Dados Azure SQL ou Para o Armazém de Dados SQL. Também explica diferentes políticas de conexão e como afeta clientes que se conectam dentro do Azure e clientes que se conectam de fora do Azure. 

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama que se segue fornece uma visão geral de alto nível da arquitetura de conectividade Azure SQL Database.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Os seguintes passos descrevem como uma ligação é estabelecida a uma base de dados Azure SQL:

- Os clientes ligam-se ao portal, que tem um endereço IP público e escuta na porta 1433.
- O portal, dependendo da política de ligação eficaz, redireciona ou proxies o tráfego para o cluster de base de dados certo.
- Dentro da base de dados o tráfego de cluster é encaminhado para a base de dados Azure SQL apropriada.

## <a name="connection-policy"></a>Política de ligação

A Base de Dados Azure SQL suporta as seguintes três opções para a definição da política de ligação de um servidor de base de dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que acolhe a base de dados, levando à redução da latência e à melhoria da sua contribuição. Para que as ligações utilizem este modo, os clientes precisam de:
   - Permitir a comunicação de saída do cliente para todos os endereços IP Azure SQL na região em portos na gama 11000 11999. Utilize as etiquetas de serviço para a SQL para facilitar a sua gestão.  
   - Permitir a comunicação de saída do cliente para os endereços IP de gateway da Base de Dados Azure SQL na porta 1433.

- **Procuração:** Neste modo, todas as ligações são proxidas através dos gateways da Base de Dados Azure SQL, levando a um aumento da latência e à redução da potência. Para que as ligações utilizem este modo, os clientes precisam de permitir a comunicação de saída do cliente para os endereços IP da Base de Dados Azure SQL na porta 1433.

- **Padrão:** Esta é a política de ligação em vigor em todos os `Proxy` servidores após a criação, a menos que altere explicitamente a política de ligação para qualquer um ou `Redirect`. A política`Redirect` padrão é para todas as ligações de clientes originárias do Azure (por exemplo, a partir de uma Máquina Virtual Azure) e `Proxy`para todas as ligações de clientes originárias do exterior (por exemplo, ligações da sua estação de trabalho local).

 Recomendamos vivamente a `Redirect` `Proxy` política de ligação sobre a política de ligação para a latência mais baixa e o maior resultado. No entanto, terá de cumprir os requisitos adicionais para permitir o tráfego de rede, tal como acima descrito. Se o cliente for uma Máquina Virtual Azure, pode fazê-lo utilizando grupos de segurança de rede (NSG) com [etiquetas de serviço](../virtual-network/security-overview.md#service-tags). Se o cliente estiver a ligar-se a partir de uma estação de trabalho no local, então poderá ter de trabalhar com o administrador da rede para permitir o tráfego de rede através da sua firewall corporativa.

## <a name="connectivity-from-within-azure"></a>Conectividade a partir de Azure

Se estiver a ligar-se a partir do Azure, as suas ligações têm uma política de ligação por `Redirect` defeito. Uma política `Redirect` de meios que, após a sessão do TCP, é estabelecida na base de dados Azure SQL, a sessão do cliente é então redirecionada para o cluster de base de dados certo com uma alteração para o IP virtual de destino da porta de entrada de base de dados Azure SQL para a do cluster. Posteriormente, todos os pacotes subsequentes fluem diretamente para o cluster, contornando o portal de bases de dados Azure SQL. O diagrama que se segue ilustra este fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora de Azure

Se estiver a ligar-se do exterior do Azure, as suas ligações têm uma política de ligação por `Proxy` defeito. Uma política `Proxy` de meios que a sessão de TCP é estabelecida através do portal de bases de dados Azure SQL e todos os pacotes subsequentes fluem através do portal. O diagrama que se segue ilustra este fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Portas TCP abertas adicionalmente 1434 e 14000-14999 para permitir [a ligação com](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac) o DAC


## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP do gateway da Base de Dados SQL do Azure

A tabela abaixo lista os endereços IP de Gateways por região. Para ligar a uma Base de Dados Azure SQL, é necessário permitir que o tráfego de rede & de **todos os** Gateways para a região.

Os pormenores de como o tráfego deve ser migrado para novas Gateways em regiões específicas estão no seguinte artigo: Migração de tráfego de base de [dados Azure SQL para gateways mais recentes](sql-database-gateway-migration.md)


| Nome da Região          | Endereços IP gateway |
| --- | --- |
| Austrália Central    | 20.36.105.0 |
| Austrália Central2   | 20.36.113.0 |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251 |
| Sul do Brasil         | 104.41.11.5, 191.233.200.14 |
| Canadá Central       | 40.85.224.249      |
| Leste do Canadá          | 40.86.226.166      |
| E.U.A. Central           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Leste da China           | 139.219.130.35     |
| China Leste 2         | 40.73.82.1         |
| Norte da China          | 139.219.15.17      |
| China Norte 2        | 40.73.50.0         |
| Ásia Leste            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| E.U.A. Leste              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| E.U.A. Leste 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| França Central       | 40.79.137.0, 40.79.129.1 |
| Alemanha Central      | 51.4.144.100       |
| Alemanha Nordeste   | 51.5.144.179       |
| Índia Central        | 104.211.96.159     |
| Índia do Sul          | 104.211.224.146    |
| Oeste da Índia           | 104.211.160.80     |
| Leste do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Oeste do Japão           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Coreia do Sul Central        | 52.231.32.42       |
| Sul da Coreia do Sul          | 52.231.200.86      |
| E.U.A. Centro-Norte     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa do Norte         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Noruega Leste          | 51.120.96.0        |
| Noroeste da Noruega          | 51.120.216.0       |
| África do Sul Norte   | 102.133.152.0      |
| África do Sul Ocidental    | 102.133.24.0       |
| E.U.A. Centro-Sul     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Ásia Sudeste      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Central dos Emirados Emirados Unidos          | 20.37.72.64        |
| Emirados Unidos norte            | 65.52.248.0        |
| Sul do Reino Unido             | 51.140.184.11      |
| Oeste do Reino Unido              | 51.141.8.11        |
| E.U.A. Centro-Oeste      | 13.78.145.25       |
| Europa ocidental          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| E.U.A. Oeste              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| E.U.A.Oeste 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como alterar a política de ligação da Base de Dados Azure SQL para um servidor de base de dados Azure SQL, consulte a [política de conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)
- Para obter informações sobre o comportamento de conexão Azure SQL Database para clientes que utilizem ADO.NET 4.5 ou uma versão posterior, consulte Portas para além de [1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações gerais sobre o desenvolvimento de aplicações, consulte a visão geral do desenvolvimento da aplicação da base de [dados SQL](sql-database-develop-overview.md).
