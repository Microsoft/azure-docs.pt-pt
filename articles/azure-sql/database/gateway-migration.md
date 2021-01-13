---
title: Aviso de migração de tráfego gateway
description: Artigo fornece aviso aos utilizadores sobre a migração de endereços IP gateway de gateway de dados Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 0542d7e0ea204d1e9279e89c9f36b9bc6c6c88fa
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165863"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migração de tráfego para gateways mais recentes
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

À medida que a infraestrutura Azure melhora, a Microsoft irá atualizar periodicamente hardware para garantir que fornecemos a melhor experiência possível ao cliente. Nos próximos meses, planeamos adicionar gateways construídos sobre as gerações de hardware mais recentes, migrar o tráfego para eles, e eventualmente desativar gateways construídos em hardware mais antigo em algumas regiões.  

Os clientes serão notificados via e-mail e no portal Azure com muita antecedência de qualquer alteração aos gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela de [endereços IP do gateway de gateway Azure SQL.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Atualizações de estado

# <a name="in-progress"></a>[Em curso](#tab/in-progress-ip)
## <a name="january-2021"></a>Janeiro de 2021
Novos Gateways SQL estão a ser adicionados às seguintes regiões:

- Austrália Central : 20.36.104.6 , 20.36.104.7 
- Austrália Central 2 : 20.36.112.6 
- Brasil Sul : 191.234.144.16 ,191.234.152.3 
- Canadá Leste : 40.69.105.9 ,40.69.105.10
- Índia Central : 104.211.86.30 , 104.211.86.31 
- Leste da Ásia : 13.75.32.14 
- França Central: 40.79.137.8, 40.79.145.12 
- França Sul : 40.79.177.10 ,40.79.177.12
- Coreia Central : 52.231.17.22 ,52.231.17.23
- Índia Oeste : 104.211.144.4

Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes em 31 de janeiro de 2021

# <a name="completed"></a>[Concluído](#tab/completed-ip)
As seguintes migrações de gateway estão completas: 

### <a name="october-2020"></a>Outubro de 2020

Novos Gateways SQL estão a ser adicionados às seguintes regiões:

- Alemanha Centro Oeste: 51.116.240.0, 51.116.248.0

Estes SQL Gateways começarão a aceitar o tráfego de clientes no dia 12 de outubro de 2020. 

### <a name="september-2020"></a>Setembro de 2020
Novos Gateways SQL estão a ser adicionados às seguintes regiões. Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes no dia **15 de setembro de 2020:**

- Sudeste da Austrália : 13.77.48.10
- Canadá Leste : 40.86.226.166, 52.242.30.154
- Reino Unido Sul : 51.140.184.11, 51.105.64.0

Os gateways SQL existentes começarão a aceitar o tráfego nas seguintes regiões. Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes no dia **15 de setembro de 2020:**

- Sudeste da Austrália : 191.239.192.109 e 13.73.109.251
- Centro dos EUA : 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 e 104.208.21.1
- Ásia Oriental : 191.234.2.139, 52.175.33.150 e 13.75.32.4
- Leste dos EUA : 40.121.158.30, 40.79.153.12, 191.238.6.43 e 40.78.225.32
- Leste DOS EUA 2 : 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 e 104.208.150.3
- França Central: 40.79.137.0 e 40.79.129.1
- Japão Oeste: 104.214.148.156, 40.74.100.192, 191.238.68.11 e 40.74.97.10
- Norte Central EUA : 23.96.178.199, 23.98.55.75 e 52.162.104.33
- Sudeste Asiático : 104.43.15.0, 23.100.117.95 e 40.78.232.3
- Oeste dos EUA: 104.42.238.205, 23.99.34.75 e 13.86.216.196

Novos Gateways SQL estão a ser adicionados às seguintes regiões. Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes no dia **10 de setembro de 2020:**

- Centro-Oeste dos EUA : 13.78.248.43 
- África do Sul Norte : 102.133.120.2  

Novos Gateways SQL estão a ser adicionados às seguintes regiões. Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes no dia **1 de setembro de 2020:**

- Norte da Europa : 13.74.104.113 
- West US2 : 40.78.248.10 
- Europa Ocidental : 52.236.184.163 
- Centro Sul dos EUA : 20.45.121.1, 20.49.88.1 

Os gateways SQL existentes começarão a aceitar o tráfego nas seguintes regiões. Estes GATEWAYS SQL devem começar a aceitar o tráfego de clientes no dia **1 de setembro de 2020:**
- Japão Leste : 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Agosto de 2020

Novos Gateways SQL estão a ser adicionados às seguintes regiões:

- Austrália Leste : 13.70.112.9
- Canadá Central : 52.246.152.0, 20.38.144.1 
- Oeste DOS EUA 2 : 40.78.240.8

Estes SQL Gateways começarão a aceitar o tráfego de clientes no dia 10 de agosto de 2020. 

### <a name="october-2019"></a>Outubro de 2019
- Sul do Brasil
- E.U.A. Oeste
- Europa Ocidental
- E.U.A. Leste
- E.U.A. Central
- Ásia Sudeste
- E.U.A. Centro-Sul
- Europa do Norte
- E.U.A. Centro-Norte
- Oeste do Japão
- Leste do Japão
- E.U.A. Leste 2
- Ásia Leste

---

## <a name="impact-of-this-change"></a>Impacto desta mudança

A migração de tráfego pode alterar o endereço IP público que o DNS resolve para a sua base de dados na Base de Dados Azure SQL.
Pode ser afetado se:

- Codificado o endereço IP para qualquer porta de entrada em particular na sua firewall no local
- Ter quaisquer sub-redes que utilizem o Microsoft.SQL como um Ponto Final de Serviço, mas não conseguem comunicar com os endereços IP gateway
- Utilize a [configuração redundante da zona para o nível de finalidade geral](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- Utilize a [configuração redundante da zona para níveis críticos de negócios premium &](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

Não será impactado se tiver:
 
- Redireccionamento como a política de ligação
- Ligações à Base de Dados SQL a partir do interior do Azure e usando Tags de Serviço
- As ligações efetuadas utilizando versões suportadas do JDBC Driver para o SQL Server não verão qualquer impacto. Para versões JDBC suportadas, consulte [Download Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se for afetado

Recomendamos que permita tráfego de saída para endereços IP para todos os [endereços IP gateway](connectivity-architecture.md#gateway-ip-addresses) na região na porta TCP 1433, e gama portuária 11000-11999. Esta recomendação aplica-se aos clientes que se ligam a partir do local e também aos que se ligam através de Pontos Endpoint de Serviço. Para obter mais informações sobre as gamas portuárias, consulte [a política de Ligação](connectivity-architecture.md#connection-policy).

As ligações efetuadas a partir de aplicações que utilizam o Controlador Microsoft JDBC abaixo da versão 4.0 podem falhar na validação do certificado. As versões mais baixas do Microsoft JDBC dependem do Nome Comum (CN) no campo assunto do certificado. A mitigação é para garantir que a propriedade do Anfitrião NatalInCertificate está definida para *.database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [Connecting with Encryption](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a mitigação acima não funcionar, arquive um pedido de suporte para a Base de Dados SQL ou para a SQL Gestão de Instâncias utilizando o seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure SQL Connectivity Architecture](connectivity-architecture.md)
