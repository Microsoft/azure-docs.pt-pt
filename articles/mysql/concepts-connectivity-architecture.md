---
title: Arquitetura de conectividade - Base de Dados Azure para MySQL
description: Descreve a arquitetura de conectividade para o seu Azure Database para o servidor MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 73178a9707d35fe7337210b11e76504794bc93ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896389"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Arquitetura de conectividade em Azure Database para MySQL
Este artigo explica a Base de Dados Azure para a arquitetura de conectividade MySQL, bem como como o tráfego é direcionado para a sua Base de Dados Azure para o caso MySQL de clientes dentro e fora de Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
A ligação à sua Base de Dados Azure para o MySQL é estabelecida através de um gateway responsável por encaminhar as ligações de entrada para a localização física do seu servidor nos nossos clusters. O diagrama seguinte ilustra o fluxo de tráfego.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Visão geral da arquitetura de conectividade":::

À medida que o cliente se liga à base de dados, obtêm uma cadeia de ligação que se liga ao portal. Este gateway tem um endereço IP público que ouve o porto 3306. Dentro do cluster de base de dados, o tráfego é encaminhado para a base de dados Azure apropriada para o MySQL. Portanto, para se conectar ao seu servidor, como a partir de redes corporativas, é necessário abrir a firewall do lado do cliente para permitir que o tráfego de saída possa chegar aos nossos gateways. Abaixo pode encontrar uma lista completa dos endereços IP utilizados pelos nossos gateways por região.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Base de Dados Azure para endereços IP de gateway MySQL
A tabela que se segue lista os IPs primários e secundários da Base de Dados Azure para a porta de entrada MySQL para todas as regiões de dados. O endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP desaudência em caso de falha da primária. Como mencionado, os clientes devem permitir a saída para ambos os endereços IP. O segundo endereço IP não escuta nenhum serviço até ser ativado pela Azure Database para o MySQL aceitar ligações.

| **Nome da Região** | **Endereços IP gateway** |
|:----------------|:-------------|
| Austrália Central| 20.36.105.0     |
| Centro da Austrália     | 20.36.113.0   |
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
| E.U.A. Leste | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113 |
| E.U.A. Leste 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| França Central | 40.79.137.0, 40.79.129.1  |
| Sul de França | 40.79.177.0     |
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
| África do Sul | 102.133.24.0   |
| E.U.A. Centro-Sul |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Ásia Sudeste | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Centro dos Emirados Árabes Unidos | 20.37.72.64  |
| Uae Norte | 65.52.248.0    |
| Sul do Reino Unido | 51.140.184.11   |
| Oeste do Reino Unido | 51.141.8.11  |
| E.U.A. Centro-Oeste | 13.78.145.25     |
| Europa Ocidental | 40.68.37.158, 191.237.232.75, 13.69.105.208 ,104.40.169.187 |
| E.U.A. Oeste | 104.42.238.205, 23.99.34.75 ,13.86.216.212, 13.86.217.212 |
| E.U.A. Oeste 2 | 13.66.226.202  |
||||

## <a name="connection-redirection"></a>Redirecionamento de conexão

A Azure Database for MySQL suporta uma política adicional de ligação, **reorientação,** que ajuda a reduzir a latência da rede entre aplicações de clientes e servidores MySQL. Com esta funcionalidade, após a sessão inicial de TCP ser estabelecida na Base de Dados Azure para o servidor MySQL, o servidor devolve o endereço de backend do nó que hospeda o servidor MySQL ao cliente. Posteriormente, todos os pacotes subsequentes fluem diretamente para o servidor, contornando o gateway. À medida que os pacotes fluem diretamente para o servidor, a latência e a produção melhoraram o desempenho.

Esta funcionalidade é suportada em Azure Database para servidores MySQL com as versões 5.6, 5.7 e 8.0.

O suporte para a reorientação está disponível na extensão php [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) desenvolvida pela Microsoft, e está disponível no [PECL](https://pecl.php.net/package/mysqlnd_azure). Consulte o artigo [de reorientação configurante](./howto-redirection.md) para obter mais informações sobre como utilizar a reorientação nas suas aplicações.

> [!IMPORTANT]
> O suporte para a reorientação na extensão [php mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) está atualmente em pré-visualização.

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir a Base de Dados Azure para as regras de firewall do MySQL utilizando o portal Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerir a Base de Dados Azure para as regras de firewall mySQL usando O Azure CLI](./howto-manage-firewall-using-cli.md)
* [Configurar reorientação com Base de Dados Azure para MySQL](./howto-redirection.md)
