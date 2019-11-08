---
title: Visão geral do desenvolvimento de aplicativos
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
ms.date: 02/07/2019
ms.openlocfilehash: af657d28bc3052ebefe25ea54891b8d3555692c9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825844"
---
# <a name="sql-database-application-development-overview"></a>Visão geral do desenvolvimento de aplicativos do banco de dados SQL

Este artigo explica as considerações básicas que um programador deve conhecer ao escrever códigos para ligar à base de dados SQL do Azure. Este artigo se aplica a todos os modelos de implantação do banco de dados SQL do Azure (banco de dados individual, pools elásticos, instância gerenciada).

> [!TIP]
> Examine os guias de introdução para bancos de dados [individuais](sql-database-single-database-quickstart-guide.md) e [instâncias gerenciadas](sql-database-managed-instance-quickstart-guide.md) se você precisar configurar seu banco de dados SQL do Azure.
>

## <a name="language-and-platform"></a>Linguagem e plataforma

Você pode usar várias [linguagens e plataformas de programação](sql-database-connect-query.md) para se conectar e consultar o banco de dados SQL do Azure. Você pode encontrar [aplicativos de exemplo](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) que você pode usar para se conectar ao banco de dados SQL do Azure.

Você pode aproveitar ferramentas de código-fonte aberto como [Cheetah](https://github.com/wunderlist/cheetah), [sql-CLI](https://www.npmjs.com/package/sql-cli) [vs Code](https://code.visualstudio.com/). Além disso, a Base de Dados SQL do Azure funciona com ferramentas da Microsoft como o [Visual Studio](https://www.visualstudio.com/downloads/) e o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Você também pode usar as APIs de portal do Azure, PowerShell e REST para ajudá-lo a obter produtividade adicional.

## <a name="authentication"></a>Autenticação

O acesso ao banco de dados SQL do Azure é protegido com logons e firewalls. O banco de dados SQL do Azure dá suporte a usuários e logons de autenticação SQL Server e [Azure Active Directory (AAD)](sql-database-aad-authentication.md) . Os logons do AAD estão disponíveis somente no Instância Gerenciada. 

Saiba mais sobre como [gerenciar o acesso ao banco de dados e o logon](sql-database-manage-logins.md).

## <a name="connections"></a>Ligações

Na sua lógica de ligação de cliente, substitua o tempo limite predefinido para 30 segundos. A predefinição de 15 segundos é demasiado curta para ligações que dependem da Internet.

Se estiver a utilizar um [conjunto de ligações](https://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que fecha a ligação assim que o seu programa não estiver a utilizá-la ativamente e não estiver a preparar-se para reutilizá-la.

Evite transações de longa execução porque qualquer falha de infraestrutura ou de conexão pode reverter a transação. Se possível, divida a transação nas várias transações menores e use o [envio em lote para melhorar o desempenho](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resiliência

O banco de dados SQL do Azure é um serviço de nuvem em que você pode esperar erros transitórios que ocorrem na infraestrutura subjacente ou na comunicação entre entidades de nuvem. Embora o banco de dados SQL do Azure seja resiliente nas falhas de infraestrutura transitivas, essas falhas podem afetar a conectividade. Quando ocorre um erro transitório durante a conexão com o banco de dados SQL, seu código deve [repetir a chamada](sql-database-connectivity-issues.md). Recomendamos que tente novamente a lógica de repetição utilize a lógica de backoff, para que não sobrecarregue a Base de Dados SQL com vários clientes em simultâneo a tentar novamente. A lógica de repetição depende das [mensagens de erro para os programas cliente do banco de dados SQL](sql-database-develop-error-messages.md).

Para obter mais informações sobre como se preparar para eventos de manutenção planejada em seu banco de dados SQL do Azure, consulte [planejando eventos de manutenção do Azure no banco de dados SQL do Azure](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Considerações de rede

- No computador que aloja o seu programa cliente, certifique-se de que a firewall permite a comunicação TCP de saída na porta 1433.  Mais informações: [configurar um firewall do banco de dados SQL do Azure](sql-database-configure-firewall-settings.md).
- Se o programa cliente se conectar ao banco de dados SQL enquanto seu cliente for executado em uma VM (máquina virtual) do Azure, você deverá abrir determinados intervalos de porta na VM. Mais informações: [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- As conexões de cliente ao banco de dados SQL do Azure às vezes ignoram o proxy e interagem diretamente com o banco de dados. As portas que não sejam 1433 tornam-se importantes. Para obter mais informações, [arquitetura de conectividade do banco de dados SQL do Azure](sql-database-connectivity-architecture.md) e [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para configuração de rede para uma instância gerenciada, consulte [configuração de rede para instâncias gerenciadas](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Passos seguintes

Explore todas as [capacidades da Base de Dados SQL](sql-database-technical-overview.md).
