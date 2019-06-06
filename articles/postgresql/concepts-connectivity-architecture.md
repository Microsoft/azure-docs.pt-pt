---
title: Arquitetura de conectividade na base de dados do Azure para PostgreSQL
description: Descreve a arquitetura de conectividade da base de dados do Azure para o servidor PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: PostgreSQL
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 73c23c471cb12ca3a3a7df4380779b464b8d86d4
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735740"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Arquitetura de conectividade na base de dados do Azure para PostgreSQL
Este artigo explica a base de dados do Azure para PostgreSQL arquitetura de conectividade, bem como a forma como o tráfego é direcionado para a sua base de dados do Azure para PostgreSQL a instância de base de dados de clientes dentro e fora do Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
Ligação à base de dados do Azure para PostgreSQL é estabelecida através de um gateway que é responsável pelo encaminhamento ligações de entrada para a localização física do seu servidor no nosso clusters. O diagrama seguinte ilustra o fluxo de tráfego.

![Descrição geral da arquitetura de conectividade](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Como cliente ligar à base de dados, eles têm uma cadeia de ligação que o liga ao gateway. Este gateway tem um endereço IP público que escuta a porta 5432. No banco de dados o tráfego de clusterz é encaminhado para o banco de dados apropriadas do Azure para PostgreSQL. Portanto, para ligar ao seu servidor, como a partir de redes corporativas, é necessário abrir a firewall do lado do cliente para permitir tráfego de saída para ser capaz de alcançar os gateways. Abaixo pode encontrar uma lista completa dos endereços IP utilizados pelo nosso gateways por região.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Base de dados do Azure para PostgreSQL endereços IP de gateway
A tabela seguinte lista os IPs primário e secundário da base de dados do Azure para PostgreSQL gateway para todas as regiões de dados. O endereço IP principal é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de ativação pós-falha em caso de falha dos principais. Conforme mencionado, os clientes devem permitir saídos para os endereços IP. O segundo endereço IP não escutar em quaisquer serviços até que seja ativado pela base de dados do Azure para PostgreSQL aceitar ligações.

| **Nome da região** | **Endereço IP primário** | **Endereço IP secundário** |
|:----------------|:-------------|:------------------------|
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
| Reino Unido Oeste | 51.141.8.11| |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| E.U.A. oeste 1 | 23.99.34.75 | 104.42.238.205 |
| EUA Oeste 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *E.U.A. Leste 2* também tem um endereço IP terciário de `52.167.104.0`.

## <a name="next-steps"></a>Passos Seguintes

* [Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](./howto-manage-firewall-using-cli.md)
