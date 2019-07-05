---
title: Aviso de migração do gateway para o Azure SQL Database de geração 2 para Gen3 | Documentos da Microsoft
description: Artigo fornece aviso aos utilizadores sobre a migração de endereços IP de Gateways de base de dados de SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538383"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego de base de dados SQL do Azure para Gateways mais recente

À medida que aumenta a infraestrutura do Azure, Microsoft atualiza periodicamente hardware para garantir que podemos fornecer a experiência do melhor possível cliente. Nos próximos meses, podemos planear adicionar que gateways criado no gerações de hardware mais recente e desativar Gateways criados no hardware mais antigo em algumas regiões.  

Os clientes serão notificados por e-mail e no portal do Azure, bem antes de qualquer alteração aos Gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na [endereços IP do gateway de base de dados do Azure SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabela.

## <a name="impact-of-this-change"></a>Esta alteração

A primeira rodada de desativação de Gateway está agendada para 1 de Setembro de 2019 nas seguintes regiões:

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

O endereço de IP desativado deixará de aceitar o tráfego e quaisquer tentativas de ligação novos serão encaminhadas para um dos Gateways na região.

Em que não verá esta alteração:

- Clientes que utilizam o redirecionamento de como a política de ligação não verá nenhum impacto.
- Ligações à base de dados SQL de dentro do Azure e a utilização de etiquetas de serviço não vão ser afetados.
- Ligações efetuadas utilizando as versões suportadas do JDBC Driver para SQL Server não irão ver nenhum impacto. Para as versões suportadas do JDBC, consulte [transferir o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer o fazer se estiver a ser afetados

Recomendamos que permite que o tráfego de saída a endereços IP para todos os [endereços IP do gateway de base de dados do Azure SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região em TCP 1433 de porta e intervalo 11000-11999 no seu dispositivo de firewall de porta. Para obter mais informações sobre intervalos de portas, consulte [política de ligação](sql-database-connectivity-architecture.md#connection-policy).

Ligações efetuadas a partir de aplicações com o controlador Microsoft JDBC inferior à versão 4.0 poderão falhar a validação do certificado. Inferior versões do Microsoft JDBC baseiam-se no nome comum (CN) no campo do requerente do certificado. A atenuação é garantir que a propriedade de hostNameInCertificate é definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [estabelecer ligação com encriptação SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a atenuação acima não funcionar, envie um pedido de suporte para a base de dados SQL com o seguinte URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)