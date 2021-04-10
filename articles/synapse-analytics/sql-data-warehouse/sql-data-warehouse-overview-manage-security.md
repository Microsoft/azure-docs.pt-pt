---
title: Garantir uma piscina SQL dedicada (anteriormente SQL DW)
description: Dicas para garantir uma piscina SQL dedicada (anteriormente SQL DW) e desenvolver soluções no Azure Synapse Analytics.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ad19c976cceab76d6eb0dbfbea5840d9764bffec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092951"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Garantir uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Visão geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo irá acompanhá-lo através dos fundamentos de garantir a sua piscina SQL dedicada (anteriormente SQL DW). Em particular, este artigo inicia-o com recursos para limitar o acesso, proteger dados e monitorizar atividades usando piscinas SQL dedicadas (anteriormente SQL DW).

## <a name="connection-security"></a>Segurança da Ligação

A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são usadas tanto pelo [servidor lógico SQL](../../azure-sql/database/logical-servers.md) como pelas suas bases de dados para rejeitar tentativas de ligação de endereços IP que não tenham sido explicitamente aprovadas. Para permitir ligações a partir da sua aplicação ou endereço IP público da sua máquina cliente, tem primeiro de criar uma regra de firewall ao nível do servidor utilizando o portal Azure, REST API ou PowerShell.

Como uma boa prática, deve restringir ao máximo as gamas de endereços IP permitidas através da firewall ao nível do servidor.  Para aceder à sua piscina SQL dedicada (anteriormente SQL DW) a partir do seu computador local, certifique-se de que a firewall da sua rede e computador local permite a comunicação de saída na porta TCP 1433.  

Piscina SQL dedicada (anteriormente SQL DW) utiliza regras de firewall IP de nível de servidor. Não suporta regras de firewall IP ao nível da base de dados. Para mais informações, consulte [as regras de firewall da Base de Dados Azure SQL](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

As ligações à sua piscina SQL dedicada (anteriormente SQL DW) são encriptadas por padrão.  A modificação das definições de ligação para desativar a encriptação é ignorada.

## <a name="authentication"></a>Autenticação

A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. Pool SQL dedicado (anteriormente SQL DW) suporta atualmente a Autenticação do Servidor SQL com um nome de utilizador e senha, e com O Diretório Ativo Azure.

Quando criou o servidor para a sua base de dados, especificou um login de "administrador do servidor" com um nome de utilizador e senha. Utilizando estas credenciais, pode autenticar em qualquer base de dados desse servidor como o proprietário da base de dados, ou "dbo" através da Autenticação do Servidor SQL.

No entanto, como uma boa prática, os utilizadores da sua organização devem usar uma conta diferente para autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividade maliciosa no caso de o seu código de aplicação estar vulnerável a um ataque de injeção DE SQL.

Para criar um utilizador autenticado pelo SERVIDOR SQL, conecte-se à base de dados **principal** do seu servidor com o seu login de administração do servidor e crie um novo login do servidor.  É uma boa ideia também criar um utilizador na base de dados principal. A criação de um utilizador em mestrado permite que um utilizador faça login usando ferramentas como SSMS sem especificar um nome de base de dados.  Também lhes permite utilizar o explorador de objetos para visualizar todas as bases de dados num servidor.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte-se ao seu **pool DE SQL dedicado (anteriormente SQL DW)** com o seu login de administração do servidor e crie um utilizador de base de dados com base no login do servidor que criou.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para dar permissão ao utilizador para realizar operações adicionais, tais como criar logins ou criar novas bases de dados, atribua o utilizador às `Loginmanager` `dbmanager` e funções na base de dados principal.

Para obter mais informações sobre estas funções adicionais e autenticação numa Base de Dados SQL, consulte [bases de dados e logins de gestão na Base de Dados Azure SQL](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Para obter mais informações sobre a ligação utilizando o Azure Ative Directory, consulte [Connecting utilizando a autenticação do Diretório Ativo Azure](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorização

A autorização refere-se ao que pode fazer dentro de uma base de dados uma vez que esteja autenticado e conectado. Os privilégios de autorização são determinados por membros e permissões de funções. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. Para gerir funções, pode utilizar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Existem formas de limitar ainda mais o que um utilizador pode fazer dentro da base de dados:

* As [Permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Granular permitem-lhe controlar quais as operações que pode fazer em colunas individuais, tabelas, vistas, esquemas, procedimentos e outros objetos na base de dados. Utilize permissões granulares para ter o maior controlo e conceder as permissões mínimas necessárias.
* [As funções de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) que não db_datareader e db_datawriter podem ser usadas para criar contas de utilizadores de aplicações mais poderosas ou contas de gestão menos potentes. As funções de base de dados fixas incorporadas proporcionam uma forma fácil de conceder permissões, mas podem resultar na concessão de mais permissões do que as necessárias.
* Os [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

O exemplo a seguir concede o acesso a um esquema definido pelo utilizador.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

A gestão de bases de dados e servidores a partir do portal Azure ou a utilização da API do Gestor de Recursos Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Encriptação

A Encriptação de Dados Transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa encriptando e desencriptando os seus dados em repouso. Quando encripta a sua base de dados, as cópias de segurança associadas e os ficheiros de registo de transações são encriptados sem que sejam necessárias quaisquer alterações nas suas aplicações. A Encriptação de Dados Transparente encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica chamada chave de encriptação de base de dados.

Na Base de Dados SQL, a chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. O algoritmo de encriptação utilizado é AES-256. Para obter uma descrição geral do TDE, consulte [a Encriptação de Dados Transparente.](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Pode encriptar a sua base de dados utilizando o [portal Azure](sql-data-warehouse-encryption-tde.md) ou [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Passos seguintes

Para obter detalhes e exemplos sobre a ligação ao seu armazém com diferentes protocolos, consulte [Connect to dedicated SQL pool (anteriormente SQL DW)](sql-data-warehouse-connect-overview.md).
