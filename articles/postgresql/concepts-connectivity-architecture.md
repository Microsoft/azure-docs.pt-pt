---
title: Arquitetura de conectividade - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a arquitetura de conectividade da sua Base de Dados Azure para PostgreSQL - Servidor Único.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 0c8f55b6eeba4319b0ce9e39085912b8c4829235
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720805"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Arquitetura de conectividade na Base de Dados Azure para PostgreSQL
Este artigo explica a Base de Dados Azure para a arquitetura de conectividade PostgreSQL, bem como como o tráfego é direcionado para a sua base de dados Azure para postgresQL caso de dados de clientes dentro e fora de Azure.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade
A ligação à sua Base de Dados Azure para PostgreSQL é estabelecida através de um gateway responsável por encaminhar as ligações de entrada para a localização física do seu servidor nos nossos clusters. O diagrama seguinte ilustra o fluxo de tráfego.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Visão geral da arquitetura de conectividade":::


À medida que o cliente se conecta à base de dados, o fio de ligação ao servidor resolve-se com o endereço IP gateway. O portal ouve no endereço IP na porta 5432. Dentro do cluster de base de dados, o tráfego é encaminhado para a base de dados Azure apropriada para postgreSQL. Por isso, para se ligar ao seu servidor, como a partir de redes corporativas, é necessário abrir a firewall do lado do cliente para permitir que o **tráfego de saída possa chegar aos nossos gateways**. Abaixo pode encontrar uma lista completa dos endereços IP utilizados pelos nossos gateways por região.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Base de Dados Azure para endereços IP de gateway postgreSQL

O serviço gateway está hospedado em grupo de nódoas apátridas sentadas atrás de um endereço IP, que o seu cliente alcançaria primeiro quando tentasse ligar-se a uma Base de Dados Azure para o servidor PostgreSQL. 

Como parte da manutenção contínua do serviço, vamos atualizar periodicamente hardware compute hospedando os gateways para garantir que fornecemos a experiência de conectividade mais segura e performante. Quando o hardware gateway é renovado, um novo anel dos nós computacional é construído primeiro. Este novo anel serve o tráfego de toda a recém-criada Base de Dados Azure para servidores PostgreSQL e terá um endereço IP diferente dos anéis de gateway mais antigos da mesma região para diferenciar o tráfego. O hardware gateway mais antigo continua a servir os servidores existentes, mas estão previstos para o desmantelamento no futuro. Antes de desativar um hardware gateway, os clientes que executam os seus servidores e se conectam a anéis de gateway mais antigos serão notificados por e-mail e no portal Azure, com três meses de antecedência antes do desmantelamento. O desmantelamento de gateways pode afetar a conectividade dos seus servidores se 

* Você codifica os endereços IP gateway na cadeia de ligação da sua aplicação. Não é **recomendado.** Deverá utilizar o nome de domínio totalmente qualificado (FQDN) do seu servidor no formato <servername> .postgres.database.azure.com, na cadeia de ligação para a sua aplicação. 
* Não atualize os novos endereços IP gateway na firewall do lado do cliente para permitir que o tráfego de saída possa chegar aos nossos novos anéis de gateway.

A tabela que se segue lista os endereços IP gateway da Base de Dados Azure para gateway PostgreSQL para todas as regiões de dados. As informações mais atualizadas dos endereços IP gateway para cada região são mantidas no quadro abaixo. Na tabela abaixo, as colunas representam o seguinte:

* **Endereços IP gateway:** Esta coluna lista os endereços IP atuais dos gateways alojados na última geração de hardware. Se estiver a a provisionar um novo servidor, recomendamos que abra a firewall do lado do cliente para permitir o tráfego de saída para os endereços IP listados nesta coluna.
* **Endereços IP gateway (desativação):** Esta coluna lista os endereços IP dos gateways alojados numa geração mais antiga de hardware que está a ser desativada neste momento. Se estiver a a provisionar um novo servidor, pode ignorar estes endereços IP. Se tiver um servidor existente, continue a manter a regra de saída para a firewall para estes endereços IP, uma vez que ainda não o desativamos. Se deixar cair as regras de firewall para estes endereços IP, poderá obter erros de conectividade. Em vez disso, espera-se que adicione proactivamente os novos endereços IP listados na coluna de endereços IP gateway à regra de firewall de saída assim que receber a notificação para desmantelamento. Isto irá garantir que quando o seu servidor é migrado para o hardware gateway mais recente, não há interrupções na conectividade com o seu servidor.
* **Endereços IP gateway (desativados):** Estas colunas listam os endereços IP dos anéis de gateway, que são desativados e já não estão em operações. Pode remover com segurança estes endereços IP da regra de firewall de saída. 


| **Nome da região** | **Endereços IP gateway** |**Endereços IP gateway (desmantelamento)** | **Endereços IP gateway (desativados)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Austrália Central| 20.36.105.0  | | |
| Centro da Austrália     | 20.36.113.0  | | |
| Leste da Austrália | 13.75.149.87, 40.79.161.1     |  | |
| Sudeste da Austrália |191.239.192.109, 13.73.109.251   |  | |
| Sul do Brasil |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Canadá Central |40.85.224.249  | | |
| Leste do Canadá | 40.86.226.166    | | |
| E.U.A. Central | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Leste da China | 139.219.130.35    | | |
| China Leste 2 | 40.73.82.1  | | |
| Norte da China | 139.219.15.17    | | |
| China Norte 2 | 40.73.50.0     | | |
| Ásia Leste | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| E.U.A. Leste |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| E.U.A. Leste 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| França Central | 40.79.137.0, 40.79.129.1  | | |
| Sul de França | 40.79.177.0     | | |
| Alemanha Central | 51.4.144.100     | | |
| Alemanha Nordeste | 51.5.144.179  | | |
| Índia Central | 104.211.96.159     | | |
| Índia do Sul | 104.211.224.146  | | |
| Oeste da Índia | 104.211.160.80    | | |
| Leste do Japão | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Oeste do Japão | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Coreia do Sul Central | 52.231.17.13   | 52.231.32.42 | |
| Sul da Coreia do Sul | 52.231.145.3     | 52.231.200.86 | |
| E.U.A. Centro-Norte | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Europa do Norte | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Norte da África do Sul  | 102.133.152.0    | | |
| Oeste da África do Sul | 102.133.24.0   | | |
| E.U.A. Centro-Sul |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Ásia Sudeste | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Centro dos Emirados Árabes Unidos | 20.37.72.64  | | |
| Uae Norte | 65.52.248.0    | | |
| Sul do Reino Unido | 51.140.184.11   | | |
| Oeste do Reino Unido | 51.141.8.11  | | |
| E.U.A. Centro-Oeste | 13.78.145.25     | | |
| Europa Ocidental |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| E.U.A. Oeste |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| E.U.A. Oeste 2 | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerir a Base de Dados Azure para regras de firewall postgreSQL utilizando o portal Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerir a Base de Dados de Azure para regras de firewall postgresQL usando Azure CLI](./howto-manage-firewall-using-cli.md)