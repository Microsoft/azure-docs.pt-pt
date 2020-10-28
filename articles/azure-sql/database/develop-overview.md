---
title: Visão geral do desenvolvimento da aplicação
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
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782982"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Visão geral do desenvolvimento de aplicações - SQL Database & SQL Managed Instance

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo percorre as considerações básicas que um desenvolvedor deve ter em conta ao escrever código para ligar à sua base de dados em Azure. Este artigo aplica-se à Base de Dados Azure SQL e à Azure SQL Managed Instance.

## <a name="language-and-platform"></a>Linguagem e plataforma

Pode utilizar [várias linguagens e plataformas de programação](connect-query-content-reference-guide.md) para conectar e consultar a Base de Dados Azure SQL. Pode encontrar aplicações de amostra que pode utilizar para ligar à base de [dados.](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)

Pode aproveitar ferramentas de código aberto como [chita,](https://github.com/wunderlist/cheetah) [sql-cli,](https://www.npmjs.com/package/sql-cli) [VS Code](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Também pode utilizar o portal Azure, PowerShell e REST APIs ajudam-no a ganhar produtividade adicional.

## <a name="authentication"></a>Autenticação

O acesso à Base de Dados Azure SQL está protegido com logins e firewalls. A Azure SQL Database suporta utilizadores e logins de autenticação do SQL Server e [do Azure Ative Directory.](authentication-aad-overview.md) Os logins do Azure Ative Directory estão disponíveis apenas em SQL Managed Instance. 

Saiba mais sobre [como gerir o acesso à base de dados e fazer login.](logins-create-manage.md)

## <a name="connections"></a>Ligações

Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos. A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.

Se estiver a utilizar um [conjunto de ligações](/dotnet/framework/data/adonet/sql-server-connection-pooling), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

Evite transações de longa duração porque qualquer falha de infraestrutura ou ligação pode reverter a transação. Se possível, divida a transação nas múltiplas transações menores e utilize [o lote para melhorar o desempenho](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Resiliência

A Azure SQL Database é um serviço de nuvem onde se podem esperar erros transitórios que ocorram na infraestrutura subjacente ou na comunicação entre entidades em nuvem. Embora o Azure SQL Database seja resistente às falhas de infraestrutura transitivas, estas falhas podem afetar a sua conectividade. Quando ocorrer um erro transitório durante a ligação à Base de Dados SQL, o seu código deve [voltar a tentar a chamada](troubleshoot-common-connectivity-issues.md). We recommend that retry logic use backoff logic, so that it does not overwhelm the service with multiple clients retrying simultaneously. A lógica de recandidúsão depende das mensagens de erro dos programas de [clientes da SQL Database](troubleshoot-common-errors-issues.md).

Para obter mais informações sobre como preparar eventos de manutenção planeados na sua Base de Dados Azure SQL, consulte [o planeamento de eventos de manutenção do Azure na Base de Dados Azure SQL](planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: [Configurar uma firewall da Base de Dados Azure SQL](firewall-configure.md).
- Se o seu programa de clientes ligar à Base de Dados SQL enquanto o seu cliente funciona numa máquina virtual Azure (VM), tem de abrir determinadas gamas de portas no VM. Mais informações: [Portos para além de 1433 para ADO.NET 4.5 e SQL Database](adonet-v12-develop-direct-route-ports.md).
- As ligações do cliente à Base de Dados Azure SQL por vezes contornam o proxy e interagem diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Para mais informações, [a Azure SQL Database arquitetura de conectividade](connectivity-architecture.md) e [portos além de 1433 para ADO.NET 4.5 e SQL Database](adonet-v12-develop-direct-route-ports.md).
- Para a configuração de rede para um exemplo de SQL Managed Instance, consulte [a configuração da rede para SQL Managed Instance](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Passos seguintes

Explore todas as capacidades da [SqL Database](sql-database-paas-overview.md) e [sql Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).

Para começar, consulte os guias da [Azure SQL Database](quickstart-content-reference-guide.md) e [das Instâncias Geridas Azure SQL](../managed-instance/quickstart-content-reference-guide.md).