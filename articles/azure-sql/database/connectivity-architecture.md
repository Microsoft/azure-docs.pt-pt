---
title: Azure SQL Database Connectivity Architecture (Arquitetura da Conectividade da Base de Dados SQL do Azure)
description: Este documento explica a arquitetura de conectividade Azure SQL Database para ligações de base de dados dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 513e198f9184d5789d5b33de7f05cda0c5dc094d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046622"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL Database e Azure Synapse Analytics conectividade arquitetura
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo explica a arquitetura de vários componentes que direcionam o tráfego de rede para um servidor na Base de Dados Azure SQL ou azure Synapse Analytics. Também explica diferentes políticas de conexão e como afeta clientes que se conectam dentro do Azure e clientes que se conectam de fora do Azure.

> [!IMPORTANT]
> Este artigo *não* se aplica à **Instância Gerida Azure SQL**. Consulte a [arquitetura de conectividade para uma instância gerida.](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama que se segue proporciona uma visão geral de alto nível da arquitetura de conectividade.

![visão geral da arquitetura](./media/connectivity-architecture/connectivity-overview.png)

Os seguintes passos descrevem como uma ligação é estabelecida à Base de Dados Azure SQL:

- Os clientes ligam-se ao portal, que tem um endereço IP público e escuta na porta 1433.
- O portal, dependendo da política de ligação eficaz, redireciona ou proxies o tráfego para o cluster de base de dados certo.
- Dentro da base de dados o tráfego de cluster é encaminhado para a base de dados apropriada.

## <a name="connection-policy"></a>Política de ligação

Os servidores da Base de Dados SQL e do Azure Synapse suportam as seguintes três opções para a definição da política de ligação do servidor:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que acolhe a base de dados, levando à redução da latência e à melhoria da sua contribuição. Para que as ligações utilizem este modo, os clientes precisam de:
  - Permitir a comunicação de saída do cliente para todos os endereços IP Azure SQL na região em portos na gama 11000 11999. Utilize as etiquetas de serviço para a SQL para facilitar a sua gestão.  
  - Permitir a comunicação de saída do cliente para os endereços IP de gateway da Base de Dados Azure SQL na porta 1433.

- **Procuração:** Neste modo, todas as ligações são proxidas através dos gateways da Base de Dados Azure SQL, levando a um aumento da latência e à redução da potência. Para que as ligações utilizem este modo, os clientes precisam de permitir a comunicação de saída do cliente para os endereços IP da Base de Dados Azure SQL na porta 1433.

- **Padrão:** Esta é a política de ligação em vigor em todos os servidores após a criação, a menos que altere explicitamente a política de ligação para `Proxy` qualquer um ou `Redirect` . A política padrão é `Redirect` para todas as ligações de clientes originárias do Azure (por exemplo, a partir de uma Máquina Virtual Azure) e `Proxy` para todas as ligações de clientes originárias do exterior (por exemplo, ligações da sua estação de trabalho local).

Recomendamos vivamente a política de ligação sobre a política de `Redirect` `Proxy` ligação para a latência mais baixa e o maior resultado. No entanto, terá de cumprir os requisitos adicionais para permitir o tráfego de rede, tal como acima descrito. Se o cliente for uma Máquina Virtual Azure, pode fazê-lo utilizando grupos de segurança de rede (NSG) com [etiquetas de serviço](../../virtual-network/security-overview.md#service-tags). Se o cliente estiver a ligar-se a partir de uma estação de trabalho no local, então poderá ter de trabalhar com o administrador da rede para permitir o tráfego de rede através da sua firewall corporativa.

## <a name="connectivity-from-within-azure"></a>Conectividade a partir de Azure

Se estiver a ligar-se a partir do Azure, as suas ligações têm uma política de ligação por `Redirect` defeito. Uma política de `Redirect` meios que, após a sessão do TCP, é estabelecida na Base de Dados Azure SQL, a sessão do cliente é então redirecionada para o cluster de base de dados certo com uma alteração para o IP virtual de destino do portal azure SQL Base de dados para o do cluster. Posteriormente, todos os pacotes subsequentes fluem diretamente para o cluster, contornando o portal de bases de dados Azure SQL. O diagrama que se segue ilustra este fluxo de tráfego.

![visão geral da arquitetura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora de Azure

Se estiver a ligar-se do exterior do Azure, as suas ligações têm uma política de ligação por `Proxy` defeito. Uma política de `Proxy` meios que a sessão de TCP é estabelecida através do portal de bases de dados Azure SQL e todos os pacotes subsequentes fluem através do portal. O diagrama que se segue ilustra este fluxo de tráfego.

![visão geral da arquitetura](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Portas TCP abertas adicionalmente 1434 e 14000-14999 para permitir [a ligação com](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac) o DAC

## <a name="gateway-ip-addresses"></a>Endereços IP gateway

A tabela abaixo lista os endereços IP de Gateways por região. Para ligar à Base de Dados SQL ou ao Azure Synapse, é necessário permitir o tráfego de rede de e para **todos os** Gateways para a região.

Os pormenores de como o tráfego deve ser migrado para novas Gateways em regiões específicas estão no seguinte artigo: Migração de tráfego de base de [dados Azure SQL para gateways mais recentes](gateway-migration.md)

| Nome da região          | Endereços IP gateway |
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

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre como alterar a política de ligação da Base de dados Azure SQL para um servidor, consulte a [política de conn.](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)
- Para obter informações sobre o comportamento de conexão Azure SQL Database para clientes que utilizem ADO.NET 4.5 ou uma versão posterior, consulte Portas para além de [1433 para ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Para obter informações gerais sobre o desenvolvimento de aplicações, consulte a visão geral do desenvolvimento da aplicação da base de [dados SQL](develop-overview.md).
