---
title: Visão geral do desenvolvimento de aplicações
description: Aprenda sobre as bibliotecas de conetividade disponíveis e as melhores práticas para as aplicações ligarem à Base de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: c2556cb1dcf59cdb8ae5014b7dd95fa2c431dc93
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050423"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Visão geral do desenvolvimento de aplicações - Base de Dados SQL & Instância Gerida SQL 
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo passa pelas considerações básicas que um desenvolvedor deve ter em conta ao escrever código para se ligar à sua base de dados em Azure. Este artigo aplica-se à Base de Dados Azure SQL e à Instância Gerida Azure SQL.

## <a name="language-and-platform"></a>Linguagem e plataforma

Pode utilizar [vários idiomas e plataformas](connect-query-content-reference-guide.md) de programação para ligar e consultar a Base de Dados Azure SQL. Pode encontrar [aplicações](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) de amostra que pode utilizar para ligar à base de dados Azure SQL.

Pode utilizar ferramentas de código aberto como [chita,](https://github.com/wunderlist/cheetah) [sql-cli,](https://www.npmjs.com/package/sql-cli) [Código VS](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Também pode utilizar o portal Azure, PowerShell e ASAP REST ajudando-o a ganhar produtividade adicional.

## <a name="authentication"></a>Autenticação

O acesso à Base de Dados Azure SQL está protegido com logins e firewalls. A Base de Dados Azure SQL suporta tanto os utilizadores de autenticação SQL Server como [azure Ative Directory](authentication-aad-overview.md) e logins. Os logins do Diretório Ativo Azure estão disponíveis apenas em Instância Gerida SQL. 

Saiba mais sobre [a gestão do acesso à base de dados e do login.](logins-create-manage.md)

## <a name="connections"></a>Ligações

Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos. A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.

Se estiver a utilizar um [conjunto de ligações](https://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

Evite transações de longo prazo porque qualquer infraestrutura ou falha de ligação pode reverter a transação. Se possível, divida a transação nas múltiplas transações menores e utilize [o loteamento para melhorar o desempenho](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Resiliência

A Base de Dados Azure SQL é um serviço na nuvem onde se podem esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades cloud. Embora a Base de Dados Azure SQL seja resiliente às falhas de infraestrutura transitivas, estas falhas podem afetar a sua conectividade. Quando ocorre um erro transitório durante a ligação à Base de Dados SQL, o seu código deve [voltar a tentar a chamada](troubleshoot-common-connectivity-issues.md). Recomendamos que a lógica de retry use a lógica de backoff, para que não sobrecarregue a base de dados SQL com vários clientes a tentarem simultaneamente. A lógica de retry depende das mensagens de erro para os programas de clientes da Base de [Dados SQL](troubleshoot-common-errors-issues.md).

Para obter mais informações sobre como se preparar para eventos de manutenção planeados na sua Base de Dados Azure SQL, consulte o planeamento de eventos de manutenção azure na Base de [Dados Azure SQL](planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: Configure uma firewall de base de [dados Azure SQL](firewall-configure.md).
- Se o seu programa de cliente se ligar à Base de Dados SQL enquanto o seu cliente funciona numa máquina virtual Azure (VM), deve abrir determinadas gamas de porta no VM. Mais informações: Portas para além de [1433 para ADO.NET 4.5 e Base de Dados SQL](adonet-v12-develop-direct-route-ports.md).
- As ligações do cliente à Base de Dados Azure SQL por vezes contornam o proxy e interagem diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Para mais informações, a arquitetura de [conectividade azure SQL Database](connectivity-architecture.md) e [os Portos para além de 1433 para ADO.NET 4.5 e SQL Database.](adonet-v12-develop-direct-route-ports.md)
- Para configurar a rede para uma instância gerida por SQL, consulte a [configuração da rede para casos geridos por SQL](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Próximos passos

Explore todas as capacidades da Base de [Dados SQL](sql-database-paas-overview.md) e da [Instância Gerida SQL](../managed-instance/sql-managed-instance-paas-overview.md).

Para começar, consulte os guias para a Base de [Dados Azure SQL](quickstart-content-reference-guide.md) e para as [instâncias geridas azure SQL](../managed-instance/quickstart-content-reference-guide.md).
