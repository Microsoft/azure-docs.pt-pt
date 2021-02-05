---
title: Arquitetura de Conectividade da Base de Dados Azure SQL
description: Este documento explica a arquitetura de conectividade Azure SQL Database para ligações de base de dados de dentro de Azure ou de fora de Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: 07303e3eea7e63f7c153db771168b8a741183362
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576503"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Arquitetura de conetividade da Base de Dados SQL do Azure e do Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo explica a arquitetura de vários componentes que direcionam o tráfego de rede para um servidor na Azure SQL Database ou Azure Synapse Analytics. Também explica diferentes políticas de conexão e como impacta os clientes que se conectam a partir de dentro do Azure e clientes que se conectam de fora da Azure.

> [!IMPORTANT]
> Este artigo *não* se aplica a **Azure SQL Managed Instance**. Consulte a [arquitetura de conectividade para um exemplo gerido.](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama seguinte fornece uma visão geral de alto nível da arquitetura de conectividade.

![Diagrama que mostra uma visão geral de alto nível da arquitetura de conectividade.](./media/connectivity-architecture/connectivity-overview.png)

Os seguintes passos descrevem como uma ligação é estabelecida com a Base de Dados Azure SQL:

- Os clientes conectam-se ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de ligação eficaz, redireciona ou proxima o tráfego para o cluster de base de dados certo.
- Dentro do tráfego de agrupamento de bases de dados é encaminhado para a base de dados apropriada.

## <a name="connection-policy"></a>Política de conexão

Os servidores da BASE de Dados SQL e do Azure Synapse suportam as seguintes três opções para a definição da política de ligação do servidor:

- **Redirecionamento (recomendado):** Os clientes estabelecem ligações diretamente ao nó que hospeda a base de dados, levando a uma redução da latência e a uma melhor produção. Para que as ligações utilizem este modo, os clientes precisam de:
  - Permitir a comunicação de saída do cliente a todos os endereços Azure SQL IP na região em portos na gama de 11000 11999. Utilize as etiquetas de serviço para SQL para facilitar a sua gestão.  
  - Permitir a comunicação de saída do cliente para os endereços IP da porta IP da base de dados Azure SQL na porta 1433.

- **Procuração:** Neste modo, todas as ligações são proximamente através dos gateways da Base de Dados Azure SQL, levando a um aumento da latência e à redução da produção. Para que as ligações utilizem este modo, os clientes precisam de permitir a comunicação de saída do cliente para os endereços IP do Gateway DE GATEWAY Azure SQL na porta 1433.

- **Predefinição:** Esta é a política de ligação em vigor em todos os servidores após a criação, a menos que altere explicitamente a política de ligação a um `Proxy` ou `Redirect` . A política padrão é `Redirect` para todas as ligações do cliente originárias do Azure (por exemplo, de uma Máquina Virtual Azure) e `Proxy` para todas as ligações do cliente originárias do exterior (por exemplo, ligações a partir da sua estação de trabalho local).

Recomendamos vivamente a `Redirect` política de ligação sobre a política de `Proxy` ligação para a menor latência e maior produção. No entanto, terá de cumprir os requisitos adicionais para permitir o tráfego da rede, tal como acima descrito. Se o cliente for uma Máquina Virtual Azure, pode fazê-lo utilizando grupos de segurança de rede (NSG) com [etiquetas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags). Se o cliente estiver a ligar-se a partir de uma estação de trabalho no local, poderá ter de trabalhar com o administrador da rede para permitir o tráfego de rede através da sua firewall corporativa.

## <a name="connectivity-from-within-azure"></a>Conectividade a partir do Azure

Se estiver a ligar a partir de dentro do Azure, as suas ligações têm uma política de ligação `Redirect` por defeito. Uma política de `Redirect` meios que após a sessão de TCP é estabelecida para Azure SQL Database, a sessão do cliente é então redirecionada para o cluster de base de dados direito com uma alteração para o destino IP virtual do de destino do gateway Azure SQL Database para o do cluster. Posteriormente, todos os pacotes subsequentes fluem diretamente para o cluster, contornando o gateway de base de dados Azure SQL. O diagrama que se segue ilustra este fluxo de tráfego.

![visão geral da arquitetura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade a partir de outro local que não o Azure

Se estiver a ligar-se de fora do Azure, as suas ligações têm uma política de ligação `Proxy` por defeito. Uma política de `Proxy` meios que a sessão de TCP é estabelecida através do gateway de base de dados Azure SQL e todos os pacotes subsequentes fluem através do gateway. O diagrama que se segue ilustra este fluxo de tráfego.

![Diagrama que mostra como a sessão TCP é estabelecida através do gateway Azure SQL Database e todos os pacotes subsequentes fluem através do gateway.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Abra ainda as portas TCP 1434 e 14000-14999 para permitir [a ligação com o CAD](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Endereços IP gateway

A tabela abaixo lista os endereços IP de Gateways por região. Para ligar à BASE de Dados SQL ou Azure Synapse, é necessário permitir o tráfego de rede de e para **todos os** Gateways para a região.

Os pormenores sobre a forma como o tráfego deve ser migrado para novos Gateways em regiões específicas constam do seguinte artigo: [Azure SQL Database migração de tráfego para gateways mais recentes](gateway-migration.md)

| Nome da região          | Endereços IP gateway |
| --- | --- |
| Austrália Central    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Austrália Central 2   | 20.36.113.0, 20.36.112.6 |
| Leste da Austrália       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Sudeste da Austrália | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Sul do Brasil         | 104.41.11.5, 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Canadá Central       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Leste do Canadá          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| E.U.A. Central           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| Leste da China           | 139.219.130.35     |
| China Leste 2         | 40.73.82.1         |
| Norte da China          | 139.219.15.17      |
| China Norte 2        | 40.73.50.0         |
| Ásia Leste            | 191.234.2.139, 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| E.U.A. Leste              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| E.U.A. Leste 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| França Central       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Sul de França         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Alemanha Central      | 51.4.144.100       |
| Alemanha Nordeste   | 51.5.144.179       |
| Alemanha Centro-Oeste | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| Índia Central        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| Índia do Sul          | 104.211.224.146    |
| Oeste da Índia           | 104.211.160.80, 104.211.144.4 |
| Leste do Japão           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Oeste do Japão           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Coreia do Sul Central        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Sul da Coreia do Sul          | 52.231.200.86      |
| E.U.A. Centro-Norte     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa do Norte         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Leste da Noruega          | 51.120.96.0        |
| Oeste da Noruega          | 51.120.216.0       |
| Norte da África do Sul   | 102.133.152.0, 102.133.120.2       |
| Oeste da África do Sul    | 102.133.24.0       |
| E.U.A. Centro-Sul     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Ásia Sudeste      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Suíça Norte    | 51.107.56.0, 51.107.57.0 |
| Suíça Oeste     | 51.107.152.0, 51.107.153.0 |
| Centro dos Emirados Árabes Unidos          | 20.37.72.64        |
| Uae Norte            | 65.52.248.0        |
| Sul do Reino Unido             | 51.140.184.11, 51.105.64.0 |
| Oeste do Reino Unido              | 51.141.8.11        |
| E.U.A. Centro-Oeste      | 13.78.145.25, 13.78.248.43        |
| Europa Ocidental          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| E.U.A. Oeste              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| E.U.A. Oeste 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como alterar a política de ligação da Base de Dados Azure SQL para um servidor, consulte [a política de conn- policy](/cli/azure/sql/server/conn-policy).
- Para obter informações sobre o comportamento de ligação da Base de Dados Azure SQL para clientes que utilizem ADO.NET 4.5 ou uma versão posterior, consulte [portões para além de 1433 para ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Para obter informações gerais sobre o desenvolvimento de aplicações, consulte a [Visão Geral do Desenvolvimento da Aplicação da Base de Dados SQL](develop-overview.md).
