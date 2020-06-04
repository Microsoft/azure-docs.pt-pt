---
title: Atualize as tabelas e firewalls de rota do Azure Databricks com os novos IPs MySQL
description: Saiba como atualizar as tabelas e firewalls de rota do Azure Databricks com novos endereços IP MySQL.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325916"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>Atualize as tabelas e firewalls de rota do Azure Databricks com os novos IPs MySQL

Todos os endereços IP da sua Base de Dados Azure para o MySQL, utilizados como metaperbaia de dados Azure, estão a mudar. Atualize as suas tabelas de rota azure Databricks ou firewalls com novos IPs MySQL antes de 30 de junho de 2020 para evitar perturbações.

Um espaço de trabalho Azure Databricks implantado na sua própria rede virtual tem uma [tabela de rotas](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) anexada. A tabela de rotas pode conter diretamente um endereço IP de metastore Azure Databricks ou uma rota para um aparelho de firewall ou proxy que possa estar a branquear o endereço.

## <a name="recommended-actions"></a>Ações recomendadas

Para evitar perturbações no seu serviço, reveja e aplique estas ações antes de 30 de junho de 2020.

* Verifique se a sua tabela de rotas está afetada por uma alteração na atualização do Endereço IP Azure MySQL.

* Utilize a tabela na secção seguinte para procurar os novos endereços IP. Para cada região, pode haver vários endereços IP utilizados como IPs primários e secundários da Base de Dados Azure para gateways MySQL. O endereço IP primário é o endereço IP atual do gateway e os segundos endereços IP são endereços IP falhados em caso de falha da primária. Para garantir a saúde do serviço, deve permitir a saída de todos os endereços IP. Se utilizar uma [metásta externa, certifique-se](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)de que um IP válido de acordo com as notificações do Azure MySQL está a ser encaminhado ou listado em branco.

* Atualize a tabela de rotas, firewall ou aparelho proxy com os novos IPs.

## <a name="updated-mysql-ip-addresses"></a>Endereços IP do MySQL atualizados

A tabela seguinte inclui todos os endereços IP que devem ser listados em branco. Endereços IP em **negrito** são os novos endereços IP. 

| Nome da região          | Endereços IP gateway                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Austrália Central    | 20.36.105.0                                                                                                |
| Austrália Central 2  | 20.36.113.0                                                                                                |
| Leste da Austrália       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Sudeste da Austrália | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Sul do Brasil         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Canadá Central       | 40.85.224.249                                                                                              |
| Leste do Canadá          | 40.86.226.166                                                                                              |
| E.U.A. Central           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| Leste da China           | 139.219.130.35                                                                                             |
| China Leste 2         | 40.73.82.1                                                                                                 |
| Norte da China          | 139.219.15.17                                                                                              |
| China Norte 2        | 40.73.50.0                                                                                                 |
| Ásia Leste            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| E.U.A. Leste              | 40.121.158.30<br>191.238.6.43                                                                              |
| E.U.A. Leste 2            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| França Central       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Alemanha Central      | 51.4.144.100                                                                                               |
| Alemanha Nordeste   | 51.5.144.179                                                                                               |
| Índia Central        | 104.211.96.159                                                                                             |
| Índia do Sul          | 104.211.224.146                                                                                            |
| Oeste da Índia           | 104.211.160.80                                                                                             |
| Leste do Japão           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Oeste do Japão           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Coreia do Sul Central        | 52.231.32.42                                                                                               |
| Sul da Coreia do Sul          | 52.231.200.86                                                                                              |
| E.U.A. Centro-Norte     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Europa do Norte         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| África do Sul Norte   | 102.133.152.0                                                                                              |
| África do Sul    | 102.133.24.0                                                                                               |
| E.U.A. Centro-Sul     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Ásia Sudeste      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| Centro dos Emirados Árabes Unidos          | 20.37.72.64                                                                                                |
| Uae Norte            | 65.52.248.0                                                                                                |
| Sul do Reino Unido             | 51.140.184.11                                                                                              |
| Oeste do Reino Unido              | 51.141.8.11                                                                                                |
| E.U.A. Centro-Oeste      | 13.78.145.25                                                                                               |
| Europa Ocidental          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| E.U.A. Oeste              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| E.U.A.Oeste 2            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Próximos passos

* [Implementar dados Azure na sua rede virtual Azure (injeção VNet)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Metastlo de Colmeia Apache Externa](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
