---
title: Proteja uma base de dados
description: Dicas para garantir uma base de dados e desenvolver soluções num recurso de piscina Synapse SQL.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: d86a0df5265418a28e1fe68de0dc2cd601e71f61
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015596"
---
# <a name="secure-a-database-in-azure-synapse"></a>Proteja uma base de dados em Azure Synapse

> [!div class="op_single_selector"]
>
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Este artigo irá acompanhá-lo através do básico de garantir a sua piscina Synapse SQL. Em particular, este artigo faz com que você tenha começado com recursos para limitar o acesso, proteger dados e monitorizar atividades numa base de dados aprovisionada usando o pool SQL.

## <a name="connection-security"></a>Segurança da Ligação

A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são usadas tanto pelo [servidor lógico SQL](../../azure-sql/database/logical-servers.md) como pelas suas bases de dados para rejeitar tentativas de ligação a partir de endereços IP que não tenham sido explicitamente listadas como brancas. Para permitir ligações a partir da sua aplicação ou endereço IP público da máquina cliente, você deve primeiro criar uma regra de firewall ao nível do servidor usando o portal Azure, REST API ou PowerShell.

Como uma boa prática, deve restringir as gamas de endereços IP permitidas através da firewall do nível do servidor o máximo possível.  Para aceder à piscina SQL a partir do seu computador local, certifique-se de que a firewall na sua rede e computador local permite a comunicação de saída na porta TCP 1433.  

O Azure Synapse Analytics utiliza regras de firewall IP ao nível do servidor. Não suporta regras de firewall IP de nível de base de dados. Para mais informações, consulte as regras de firewall da Base de [Dados Azure SQL](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

As ligações à sua piscina SQL são encriptadas por padrão.  A modificação das definições de ligação para desativar a encriptação é ignorada.

## <a name="authentication"></a>Autenticação

A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. O pool SQL suporta atualmente a Autenticação do Servidor SQL com um nome de utilizador e senha, e com diretório Ativo Azure.

Quando criou o servidor para a sua base de dados, especificou um login "server admin" com um nome de utilizador e uma palavra-passe. Utilizando estas credenciais, pode autenticar qualquer base de dados desse servidor como proprietário da base de dados, ou "dbo" através da Autenticação do Servidor SQL.

No entanto, como uma boa prática, os utilizadores da sua organização devem usar uma conta diferente para autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividade maliciosa caso o seu código de aplicação seja vulnerável a um ataque de injeção SQL.

Para criar um utilizador Autenticado do Servidor SQL, ligue-se à base de dados **principal** do seu servidor com o seu servidor e crie um novo login do servidor.  É uma boa ideia também criar um utilizador na base de dados principal. A criação de um utilizador em mestrado permite que um utilizador inicie sessão utilizando ferramentas como O SSMS sem especificar um nome de base de dados.  Também permite que utilizem o explorador de objetos para visualizar todas as bases de dados de um servidor.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, ligue-se à sua base de dados de **piscina SQL** com o seu servidor de login e crie um utilizador de base de dados com base no login do servidor que criou.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para dar permissão ao utilizador para realizar operações adicionais, tais como a criação de logins ou a criação de novas bases de dados, atribuir o utilizador às `Loginmanager` funções e funções na base de `dbmanager` dados principal.

Para obter mais informações sobre estas funções adicionais e autenticar uma base de dados SQL, consulte [a Managing database e os logins na Base de Dados Azure SQL](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Para obter mais informações sobre a ligação utilizando o Diretório Ativo Azure, consulte a Ligação utilizando a [autenticação de diretório ativo Azure](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorização

A autorização refere-se ao que pode fazer dentro de uma base de dados uma vez autenticado e conectado. Os privilégios de autorização são determinados por membros e permissões de papéis. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. Para gerir as funções, pode utilizar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Existem formas de limitar ainda mais o que um utilizador pode fazer dentro da base de dados:

* As [Permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Granular permitem controlar quais as operações que pode fazer em colunas individuais, tabelas, vistas, esquemas, procedimentos e outros objetos na base de dados. Utilize permissões granulares para ter o maior controlo e conceder as permissões mínimas necessárias.
* [Funções](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) de base de dados que não db_datareader e db_datawriter podem ser usadas para criar contas de utilizadores de aplicações mais poderosas ou contas de gestão menos poderosas. As funções de base de dados fixas incorporadas fornecem uma forma fácil de conceder permissões, mas podem resultar na concessão de mais permissões do que as necessárias.
* Os [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

O exemplo seguinte dá acesso a um esquema definido pelo utilizador.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

A gestão de bases de dados e servidores do portal Azure ou utilizando a API do Gestor de Recursos Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para mais informações, consulte o [controlo de acesso baseado em role no portal Azure](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Encriptação

A Encriptação transparente de dados (TDE) ajuda a proteger contra a ameaça de atividades maliciosas encriptando e desencriptando os seus dados em repouso. Quando encripta a sua base de dados, as cópias de segurança associadas e os ficheiros de registo de transações são encriptados sem que seja necessário alterar as suas aplicações. A Encriptação de Dados Transparente encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica chamada chave de encriptação de base de dados.

Na Base de Dados SQL, a chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. O algoritmo de encriptação utilizado é AES-256. Para obter uma descrição geral do TDE, consulte a [Encriptação transparente de dados](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Pode encriptar a sua base de dados através do [portal Azure](sql-data-warehouse-encryption-tde.md) ou [DoT T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Próximos passos

Para mais detalhes e exemplos sobre a ligação ao seu armazém com diferentes protocolos, consulte [Connect to SQL pool](../sql/connect-overview.md).
