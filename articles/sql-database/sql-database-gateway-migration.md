---
title: Aviso de migração de tráfego de gateway
description: O artigo fornece um aviso aos usuários sobre a migração de endereços IP de gateways do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: fe35dc4c22f3852934cde0d6f33084b56266d514
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807692"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego do banco de dados SQL do Azure para gateways mais recentes

À medida que a infraestrutura do Azure melhora, a Microsoft atualizará periodicamente o hardware para garantir que forneceremos a melhor experiência possível para o cliente. Nos próximos meses, planejamos adicionar gateways criados em gerações de hardware mais recentes, migrar o tráfego para eles e eventualmente descomissionar os gateways criados em um hardware mais antigo em algumas regiões.  

Os clientes serão notificados por email e na portal do Azure bem antes de qualquer alteração nos gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela [endereços IP do gateway do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Impacto dessa alteração

A primeira rodada de migração de tráfego para gateways mais recentes está agendada para **14 de outubro de 2019** nas seguintes regiões:
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

A migração de tráfego alterará o endereço IP público que o DNS resolve para o banco de dados SQL.
Você será afetado se tiver:
- O endereço IP embutido em código para qualquer gateway específico em seu firewall local
- Todas as sub-redes que usam Microsoft. SQL como um ponto de extremidade de serviço, mas não podem se comunicar com os endereços IP do gateway

Você não será afetado se tiver:
- Redirecionamento como a política de conexão
- Conexões com o banco de dados SQL de dentro do Azure e usando marcas de serviço
- As conexões feitas usando as versões com suporte do driver JDBC para SQL Server não terão impacto. Para obter suporte para versões JDBC, consulte [baixar o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se você for afetado

Recomendamos que você permita o tráfego de saída para endereços IP para todos os [endereços IP do gateway do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região na porta TCP 1433 e o intervalo de porta 11000-11999. Essa recomendação é aplicável aos clientes que se conectam do local e também aos que se conectam por meio de pontos de extremidade de serviço. Para obter mais informações sobre intervalos de portas, consulte [política de conexão](sql-database-connectivity-architecture.md#connection-policy).

As conexões feitas de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4,0 podem falhar na validação do certificado. Versões inferiores do Microsoft JDBC dependem do nome comum (CN) no campo assunto do certificado. A mitigação é garantir que a propriedade hostNameInCertificate esteja definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [conectando-se com a criptografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a atenuação acima não funcionar, entre em arquivo uma solicitação de suporte para o banco de dados SQL usando a seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)
