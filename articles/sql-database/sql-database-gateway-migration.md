---
title: Aviso de migração de tráfego de gateway
description: Artigo dá aviso aos utilizadores sobre a migração de endereços IP da Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757083"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego de bases de dados Azure SQL para gateways mais recentes

À medida que a infraestrutura azure melhora, a Microsoft irá atualizar periodicamente o hardware para garantir que fornecemos a melhor experiência possível ao cliente. Nos próximos meses, planeamos adicionar Gateways construídos sobre novas gerações de hardware, migrar tráfego para eles, e eventualmente desmantelar Gateways construídos em hardware mais antigo em algumas regiões.  

Os clientes serão notificados via e-mail e no portal Azure bem antes de qualquer alteração aos Gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela de endereços IP da Base de [Dados Azure SQL.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Impacto desta mudança

A primeira ronda de migração de tráfego para gateways mais recentes está marcada para 14 de outubro de **2019** nas seguintes regiões:
- Sul do Brasil
- E.U.A. Oeste
- Europa ocidental
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

A migração de tráfego mudará o endereço IP público que o DNS resolve para a sua Base de Dados SQL.
Será impactado se tiver:
- Código duro o endereço IP para qualquer Gateway específico na sua firewall no local
- Quaisquer subredes que utilizem o Microsoft.SQL como ponto final de serviço, mas não conseguem comunicar com os endereços IP gateway

Não será afetado se tiver:
- Reorientação como política de ligação
- Conexões à Base de Dados SQL a partir de dentro do Azure e utilização de etiquetas de serviço
- As ligações efetuadas utilizando versões suportadas do Condutor JDBC para o Servidor SQL não verão qualquer impacto. Para versões JDBC suportadas, consulte [Download Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se for afetado

Recomendamos que permita tráfego de saída para endereços IP para todos os endereços IP de [gateway azure SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região na porta TCP 1433, e alcance de porta 11000-11999. Esta recomendação aplica-se aos clientes que se conectam a partir do local e também aos que se ligam através de Pontos Finais de Serviço. Para obter mais informações sobre as gamas portuárias, consulte a política de [ligação.](sql-database-connectivity-architecture.md#connection-policy)

As ligações efetuadas a partir de aplicações que utilizem o Microsoft JDBC Driver abaixo da versão 4.0 podem falhar a validação do certificado. Versões mais baixas do Microsoft JDBC dependem do Nome Comum (CN) no campo Assunto do certificado. A mitigação é para garantir que a propriedade hostNameInCertificate está definida para *.database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [A Ligação com encriptação](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a mitigação acima não funcionar, apresente um pedido de suporte para a Base de Dados SQL utilizando o seguinte URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Arquitetura de Conectividade Azure SQL](sql-database-connectivity-architecture.md)
