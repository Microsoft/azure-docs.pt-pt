---
title: Arquitetura de conectividade-banco de dados do Azure para MySQL
description: Descreve a arquitetura de conectividade do banco de dados do Azure para o servidor MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 22c77bee95533606156ec6cc337af1d743018005
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765329"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Arquitetura de conectividade no banco de dados do Azure para MySQL
Este artigo explica a arquitetura de conectividade do banco de dados do Azure para MySQL e também como o tráfego é direcionado para a instância do banco de dados do Azure para MySQL de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
A conexão com o banco de dados do Azure para MySQL é estabelecida por meio de um gateway que é responsável por rotear conexões de entrada para o local físico do servidor em nossos clusters. O diagrama a seguir ilustra o fluxo de tráfego.

![Visão geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Como o cliente se conecta ao banco de dados, ele obtém uma cadeia de conexão que se conecta ao gateway. Esse gateway tem um endereço IP público que escuta a porta 3306. Dentro do cluster de banco de dados, o tráfego é encaminhado para o banco de dados do Azure apropriado para MySQL. Portanto, para se conectar ao servidor, como de redes corporativas, é necessário abrir o Firewall do lado do cliente para permitir que o tráfego de saída seja capaz de alcançar nossos gateways. Abaixo, você pode encontrar uma lista completa dos endereços IP usados por nossos gateways por região.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Banco de dados do Azure para endereços IP do gateway MySQL
A tabela a seguir lista os IPs primários e secundários do banco de dados do Azure para o gateway MySQL para todas as regiões de data. O endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de failover em caso de falha do primário. Conforme mencionado, os clientes devem permitir a saída para os dois endereços IP. O segundo endereço IP não escuta em nenhum serviço até que seja ativado pelo banco de dados do Azure para MySQL para aceitar conexões.

| **Nome da região** | **Endereço IP primário** | **Endereço IP secundário** |
|:----------------|:-------------|:------------------------|
| Leste da Austrália | 13.75.149.87 | 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| Centro dos E.U.A. | 23.99.160.139 | 13.67.215.62 |
| Leste da China 1 | 139.219.130.35 | |
| Leste da China 2 | 40.73.82.1 | |
| Norte da China 1 | 139.219.15.17 | |
| Norte da China 2 | 40.73.50.0 | |
| Este Asiático | 191.234.2.139 | 52.175.33.150 |
| Leste dos EUA 1 | 191.238.6.43 | 40.121.158.30 |
| Este dos E.U.A. 2 | 191.239.224.107 | 40.79.84.180 * |
| França Central | 40.79.137.0 | 40.79.129.1 |
| Nordeste da Alemanha | 51.4.144.100 | |
| Índia Central | 104.211.96.159 | |
| Índia do Sul | 104.211.224.146 | |
| Oeste da Índia | 104.211.160.80 | |
| Este do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia do Sul Central | 52.231.32.42 | |
| Sul da Coreia do Sul | 52.231.200.86 |  |
| E.U.A. Centro-Norte | 23.98.55.75 | 23.96.178.199 |
| Europa do Norte | 191.235.193.75 | 40.113.93.91 |
| E.U.A. Centro-Sul | 23.98.162.75 | 13.66.62.124 |
| Sudeste Asiático | 23.100.117.95 | 104.43.15.0 |
| Norte da África do Sul | 102.133.152.0 | |
| Oeste da África do Sul | 102.133.24.0 | |
| Norte dos E.A.U. | 65.52.248.0 | |
| Sul do Reino Unido | 51.140.184.11 | |
| Oeste do Reino Unido | 51.141.8.11| |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| Oeste dos EUA 1 | 23.99.34.75 | 104.42.238.205 |
| E.U.A. Oeste 2 | 13.66.226.202 | |
||||

> [!NOTE]
> O *leste dos EUA 2* também tem um endereço IP terciário de `52.167.104.0`.

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o CLI do Azure](./howto-manage-firewall-using-cli.md)