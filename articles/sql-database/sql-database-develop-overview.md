---
title: Descrição Geral da Programação de Aplicações de Bases de Dados SQL | Microsoft Docs
description: Aprenda sobre as bibliotecas de conetividade disponíveis e as melhores práticas para as aplicações ligarem à Base de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 6ad8079985925d4b9bf74b22971ca3c387af199a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455620"
---
# <a name="sql-database-application-development-overview"></a>Descrição geral da programação de aplicativo da linha de base de dados SQL

Este artigo explica as considerações básicas que um programador deve conhecer ao escrever códigos para ligar à base de dados SQL do Azure. Este artigo se aplica a todos os modelos de implementação do Azure SQL Database (base de dados, conjuntos elásticos, a instância gerida).

> [!TIP]
> Examinando a obtenção de guias para a utilizar [bases de dados únicas](sql-database-single-database-quickstart-guide.md) e [instâncias geridas](sql-database-managed-instance-quickstart-guide.md) se precisar de configurar a sua base de dados do SQL do Azure.
>

## <a name="language-and-platform"></a>Linguagem e plataforma

Pode usar várias [plataformas e linguagens de programação](sql-database-connect-query.md) para ligar e consultar a base de dados do Azure SQL. Pode encontrar [aplicações de exemplo](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) que pode utilizar para ligar à base de dados SQL do Azure.

Pode tirar partido de ferramentas de código-fonte aberto, como [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Também pode utilizar o portal do Azure, PowerShell e REST APIs ajudá-lo a obter produtividade adicional.

## <a name="authentication"></a>Authentication

Acesso à base de dados do Azure SQL está protegido com inícios de sessão e firewalls. Base de dados SQL do Azure suporta tanto o SQL Server e [autenticação do Azure Active Directory (AAD)](sql-database-aad-authentication.md) utilizadores e inícios de sessão. Inícios de sessão do AAD estão disponíveis apenas na instância gerida. 

Saiba mais sobre [gestão de acesso de base de dados e de início de sessão](sql-database-manage-logins.md).

## <a name="connections"></a>Ligações

Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos. A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.

Se estiver a utilizar um [conjunto de ligações](https://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

Evite as transações de longa execução porque qualquer falha de ligação ou a infraestrutura pode reverter a transação. Se possível, dividir a transação em várias transações de menores e utilizar [para melhorar o desempenho da criação de batches](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resiliência

Base de dados SQL do Azure é um serviço cloud em que pode esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades da cloud. Embora a SQL Database do Azure é resiliente em falhas de infraestrutura transitivo, estas falhas podem afetar a conectividade. Quando ocorre um erro transitório ao ligar à base de dados SQL, seu código deve [repetir a chamada](sql-database-connectivity-issues.md). Recomendamos que tente novamente a lógica de repetição utilize a lógica de backoff, para que não sobrecarregue a Base de Dados SQL com vários clientes em simultâneo a tentar novamente. A lógica de repetição depende da [mensagens de erro para os programas de cliente de base de dados SQL](sql-database-develop-error-messages.md).

Para obter mais informações sobre como preparar para eventos de manutenção planeada na sua base de dados SQL do Azure, consulte [planejamento para eventos de manutenção do Azure na base de dados do Azure SQL](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: [Configurar uma firewall de base de dados do Azure SQL](sql-database-configure-firewall-settings.md).
- Se o seu programa cliente se liga à base de dados do SQL, enquanto o cliente executa numa máquina virtual do Azure (VM), tem de abrir determinados intervalos de portas na VM. Mais informações: [Portas para além do 1433 para ADO.NET 4.5 e base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Ligações de cliente para a base de dados do Azure SQL, às vezes, ignoram o proxy e interagem diretamente com a base de dados. As portas que não sejam 1433 tornam-se importantes. Para obter mais informações, [arquitetura de conectividade do Azure SQL Database](sql-database-connectivity-architecture.md) e [portas para além do 1433 para ADO.NET 4.5 e base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para a configuração de rede para uma instância gerida, veja [configuração de rede para instâncias geridas](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Passos Seguintes

Explore todas as [capacidades da Base de Dados SQL](sql-database-technical-overview.md).
