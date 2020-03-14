---
title: Visão geral do desenvolvimento de aplicações
description: Aprenda sobre as bibliotecas de conetividade disponíveis e as melhores práticas para as aplicações ligarem à Base de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.openlocfilehash: 26aa9948a44727ff4c8092eb5131b1c054bf5442
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257007"
---
# <a name="sql-database-application-development-overview"></a>Visão geral do desenvolvimento da aplicação da base de dados SQL

Este artigo explica as considerações básicas que um programador deve conhecer ao escrever códigos para ligar à base de dados SQL do Azure. Este artigo aplica-se a todos os modelos de implantação da Base de Dados Azure SQL (base de dados única, piscinas elásticas, instância gerida).

> [!TIP]
> Veja os guias iniciados para bases de [dados individuais](sql-database-single-database-quickstart-guide.md) e [casos geridos](sql-database-managed-instance-quickstart-guide.md) se precisar de configurar a sua Base de Dados Azure SQL.
>

## <a name="language-and-platform"></a>Linguagem e plataforma

Pode utilizar [vários idiomas e plataformas](sql-database-connect-query.md) de programação para ligar e consultar a Base de Dados Azure SQL. Pode encontrar [aplicações](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) de amostra que pode utilizar para ligar à Base de Dados Azure SQL.

Pode utilizar ferramentas de código aberto como [chita,](https://github.com/wunderlist/cheetah) [sql-cli,](https://www.npmjs.com/package/sql-cli) [Código VS](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Também pode utilizar o portal Azure, PowerShell e ASAP REST ajudando-o a ganhar produtividade adicional.

## <a name="authentication"></a>Autenticação

O acesso à Base de Dados Azure SQL está protegido com logins e firewalls. A Base de Dados Azure SQL suporta tanto os utilizadores de autenticação sQL Server como [Azure Ative Directory (AAD)](sql-database-aad-authentication.md) e os logins. Os logins AAD estão disponíveis apenas em Caso Gerido. 

Saiba mais sobre [a gestão do acesso à base de dados e do login.](sql-database-manage-logins.md)

## <a name="connections"></a>Ligações

Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos. A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.

Se estiver a utilizar um [conjunto de ligações](https://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

Evite transações de longo prazo porque qualquer infraestrutura ou falha de ligação pode reverter a transação. Se possível, divida a transação nas múltiplas transações menores e utilize [o loteamento para melhorar o desempenho](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resiliência

A Base de Dados Azure SQL é um serviço na nuvem onde se podem esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades cloud. Embora a Base de Dados Azure SQL seja resiliente às falhas de infraestrutura transitivas, estas falhas podem afetar a sua conectividade. Quando ocorre um erro transitório durante a ligação à Base de Dados SQL, o seu código deve [voltar a tentar a chamada](sql-database-connectivity-issues.md). Recomendamos que tente novamente a lógica de repetição utilize a lógica de backoff, para que não sobrecarregue a Base de Dados SQL com vários clientes em simultâneo a tentar novamente. A lógica de retry depende das mensagens de erro para os programas de clientes da Base de [Dados SQL](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md).

Para obter mais informações sobre como se preparar para eventos de manutenção planeados na sua base de dados Azure SQL, consulte o planeamento de eventos de manutenção do Azure na Base de [Dados Azure SQL](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: Configure uma firewall de base de [dados Azure SQL](sql-database-configure-firewall-settings.md).
- Se o seu programa de cliente se ligar à Base de Dados SQL enquanto o seu cliente funciona numa máquina virtual Azure (VM), deve abrir determinadas gamas de porta no VM. Mais informações: Portas para além de [1433 para ADO.NET 4.5 e Base de Dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- As ligações do cliente à Base de Dados Azure SQL por vezes contornam o proxy e interagem diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Para mais informações, a arquitetura de [conectividade azure SQL Database](sql-database-connectivity-architecture.md) e [os Portos para além de 1433 para ADO.NET 4.5 e SQL Database.](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para configurar a rede para uma instância gerida, consulte a [configuração da rede para casos geridos](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Passos seguintes

Explore todas as [capacidades da Base de Dados SQL](sql-database-technical-overview.md).
