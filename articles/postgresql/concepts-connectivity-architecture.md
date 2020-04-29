---
title: Arquitetura de conectividade - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a arquitetura de conectividade da sua Base de Dados Azure para PostgreSQL - Servidor Único.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: d23a59e97ee3fc935a0d0954bc70b547b727fddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546600"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Arquitetura de conectividade na Base de Dados Azure para PostgreSQL
Este artigo explica a Base de Dados Azure para a arquitetura de conectividade PostgreSQL, bem como como o tráfego é direcionado para a sua base de dados Azure para a instância de base de dados PostgreSQL de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
A ligação à sua Base de Dados Azure para PostgreSQL é estabelecida através de uma porta de entrada que é responsável por encaminhar as ligações de entrada para a localização física do seu servidor nos nossos clusters. O diagrama que se segue ilustra o fluxo de tráfego.

![Visão geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

À medida que o cliente se liga à base de dados, eles recebem uma cadeia de ligação que se liga ao portal. Este portal tem um endereço IP público que escuta o porto 5432. Dentro da base de dados o tráfego de cluster é encaminhado para base de dados Azure apropriada para PostgreSQL. Por isso, para se ligar ao seu servidor, como por exemplo a partir de redes corporativas, é necessário abrir a firewall do lado do cliente para permitir que o tráfego de saída possa chegar aos nossos gateways. Abaixo pode encontrar uma lista completa dos endereços IP utilizados pelos nossos gateways por região.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Base de Dados Azure para endereços IP de gateway PostgreSQL
A tabela seguinte lista os IPs primários e secundários da Base de Dados Azure para gateway PostgreSQL para todas as regiões de dados. O endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP failover em caso de falha do primário. Como mencionado, os clientes devem permitir a saída para ambos os endereços IP. O segundo endereço IP não ouve nenhum serviço até ser ativado pela Base de Dados Azure para o PostgreSQL aceitar ligações.

| **Nome da Região** | **Endereços IP gateway** |
|:----------------|:-------------|
| Austrália Central| 20.36.105.0     |
| Austrália Central2     | 20.36.113.0   |
| Leste da Austrália | 13.75.149.87, 40.79.161.1     |
| Sudeste da Austrália |191.239.192.109, 13.73.109.251   |
| Sul do Brasil | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Canadá Central |40.85.224.249  |
| Leste do Canadá | 40.86.226.166    |
| E.U.A. Central | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Leste da China | 139.219.130.35    |
| China Leste 2 | 40.73.82.1  |
| Norte da China | 139.219.15.17    |
| China Norte 2 | 40.73.50.0     |
| Ásia Leste | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| E.U.A. Leste | 40.121.158.30, 191.238.6.43  |
| E.U.A. Leste 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| França Central | 40.79.137.0, 40.79.129.1  |
| Alemanha Central | 51.4.144.100     |
| Alemanha Nordeste | 51.5.144.179  |
| Índia Central | 104.211.96.159     |
| Índia do Sul | 104.211.224.146  |
| Oeste da Índia | 104.211.160.80    |
| Leste do Japão | 13.78.61.196, 191.237.240.43  |
| Oeste do Japão | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Coreia do Sul Central | 52.231.32.42   |
| Sul da Coreia do Sul | 52.231.200.86    |
| E.U.A. Centro-Norte | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa do Norte | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| África do Sul Norte  | 102.133.152.0    |
| África do Sul Ocidental | 102.133.24.0   |
| E.U.A. Centro-Sul |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Ásia Sudeste | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Central dos Emirados Emirados Unidos | 20.37.72.64  |
| Emirados Unidos norte | 65.52.248.0    |
| Sul do Reino Unido | 51.140.184.11   |
| Oeste do Reino Unido | 51.141.8.11  |
| E.U.A. Centro-Oeste | 13.78.145.25     |
| Europa ocidental | 40.68.37.158, 191.237.232.75, 13.69.105.208  |
| E.U.A. Oeste | 104.42.238.205, 23.99.34.75  |
| E.U.A.Oeste 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Passos seguintes

* [Crie e gerea Base de Dados Azure para regras de firewall PostgreSQL usando o portal Azure](./howto-manage-firewall-using-portal.md)
* [Crie e gerea Base de Dados Azure para regras de firewall PostgreSQL usando o Azure CLI](./howto-manage-firewall-using-cli.md)
