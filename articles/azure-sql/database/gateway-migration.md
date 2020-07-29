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
ms.openlocfilehash: 22bfab5b9f00a392054fa1aef6a93195180fd968
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373492"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migração de tráfego para gateways mais recentes
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

À medida que a infraestrutura Azure melhora, a Microsoft irá atualizar periodicamente hardware para garantir que fornecemos a melhor experiência possível ao cliente. Nos próximos meses, planeamos adicionar gateways construídos sobre as gerações de hardware mais recentes, migrar o tráfego para eles, e eventualmente desativar gateways construídos em hardware mais antigo em algumas regiões.  

Os clientes serão notificados via e-mail e no portal Azure com muita antecedência de qualquer alteração aos gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela de [endereços IP do gateway de gateway Azure SQL.](connectivity-architecture.md#gateway-ip-addresses)

## <a name="status-updates"></a>Atualizações de estado

# <a name="in-progress"></a>[Em curso](#tab/in-progress-ip)
### <a name="september-2020"></a>Setembro de 2020

Novos Gateways SQL estão a ser adicionados às seguintes regiões:

- Norte da Europa : 13.74.104.113 
- West US2 : 40.78.248.10 
- Europa Ocidental : 52.236.184.163 
- Centro Sul dos EUA : 20.45.121.1, 20.49.88.1 

Os gateways SQL existentes começarão a aceitar o tráfego nas seguintes regiões:
- Japão Leste : 40.79.184.8, 40.79.192.5

Estes SQL Gateways começarão a aceitar o tráfego de clientes no dia 1 de setembro de 2020. 

### <a name="august-2020"></a>agosto de 2020

Novos Gateways SQL estão a ser adicionados às seguintes regiões:

- Austrália Leste : 13.70.112.9
- Canadá Central : 52.246.152.0, 20.38.144.1 
- Oeste DOS EUA 2 : 40.78.240.8

Estes SQL Gateways começarão a aceitar o tráfego de clientes no dia 10 de agosto de 2020. 

# <a name="completed"></a>[Concluído](#tab/completed-ip)

As seguintes migrações de gateway estão completas: 

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
- Ter quaisquer sub-redes que utilizem o Microsoft.SQL como um Ponto Final de Serviço, mas não podem comunicar com os endereços IP gateway
- Utilize a [configuração redundante](high-availability-sla.md#zone-redundant-configuration) da zona para a sua base de dados

Não será impactado se tiver:

- Redireccionamento como a política de ligação
- Ligações à Base de Dados SQL a partir do interior do Azure e usando Tags de Serviço
- As ligações efetuadas utilizando versões suportadas do JDBC Driver para o SQL Server não verão qualquer impacto. Para versões JDBC suportadas, consulte [Download Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se for afetado

Recomendamos que permita tráfego de saída para endereços IP para todos os [endereços IP gateway](connectivity-architecture.md#gateway-ip-addresses) na região na porta TCP 1433, e gama portuária 11000-11999. Esta recomendação aplica-se aos clientes que se ligam a partir do local e também aos que se ligam através de Pontos Endpoint de Serviço. Para obter mais informações sobre as gamas portuárias, consulte [a política de Ligação](connectivity-architecture.md#connection-policy).

As ligações efetuadas a partir de aplicações que utilizam o Controlador Microsoft JDBC abaixo da versão 4.0 podem falhar na validação do certificado. As versões mais baixas do Microsoft JDBC dependem do Nome Comum (CN) no campo assunto do certificado. A mitigação é para garantir que a propriedade do Anfitrião NatalInCertificate está definida para *.database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [Connecting with Encryption](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a mitigação acima não funcionar, arquive um pedido de suporte para a Base de Dados SQL ou para a SQL Gestão de Instâncias utilizando o seguinte URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Azure SQL Connectivity Architecture](connectivity-architecture.md)
