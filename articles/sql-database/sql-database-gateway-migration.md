---
title: Aviso de migração de gateway para o banco de dados SQL do Azure de Gen2 para Gen3 | Microsoft Docs
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
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568123"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego do banco de dados SQL do Azure para gateways mais recentes

À medida que a infraestrutura do Azure melhora, a Microsoft atualizará periodicamente o hardware para garantir que forneceremos a melhor experiência possível para o cliente. Nos próximos meses, planejamos adicionar gateways criados em gerações de hardware mais recentes e descomissionar gateways criados em hardware mais antigo em algumas regiões.  

Os clientes serão notificados por email e na portal do Azure bem antes de qualquer alteração nos gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela [endereços IP do gateway do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Impacto dessa alteração

A primeira rodada de descomissionamento de gateway está agendada para 1º de setembro de 2019 nas seguintes regiões:

- EUA Oeste
- Europa Ocidental
- East US
- EUA Central
- Sudeste Asiático
- EUA Centro-Sul
- Europa do Norte
- EUA Centro-Norte
- Oeste do Japão
- Leste do Japão
- EUA Leste 2
- Ásia Oriental

O endereço IP descomissionado deixará de aceitar o tráfego e quaisquer novas tentativas de conexão serão roteadas para um dos gateways na região.

Onde você não verá o impacto dessa alteração:

- Os clientes que usam o redirecionamento como sua política de conexão não verão nenhum impacto.
- As conexões com o banco de dados SQL de dentro do Azure e o uso de marcas de serviço não serão afetadas.
- As conexões feitas usando as versões com suporte do driver JDBC para SQL Server não terão impacto. Para obter suporte para versões JDBC, consulte [baixar o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se você for afetado

Recomendamos que você permita o tráfego de saída para endereços IP para todos os [endereços IP do gateway do banco de dados SQL do Azure](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região na porta TCP 1433 e o intervalo de portas 11000-11999 no seu dispositivo de firewall. Para obter mais informações sobre intervalos de portas, consulte [política de conexão](sql-database-connectivity-architecture.md#connection-policy).

As conexões feitas de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4,0 podem falhar na validação do certificado. Versões inferiores do Microsoft JDBC dependem do nome comum (CN) no campo assunto do certificado. A mitigação é garantir que a propriedade hostNameInCertificate esteja definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [conectando-se com a criptografia SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a atenuação acima não funcionar, entre em arquivo uma solicitação de suporte para o banco de dados SQL usando a seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)