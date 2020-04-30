---
title: Autorizar o acesso ao servidor e à base de dados utilizando logins e contas de utilizador
description: Saiba como a Azure SQL Database e a Azure Synapse Analytics autenticam os utilizadores para acesso através de logins e contas de utilizador. Também saiba como usar as funções de base de dados e permissões explícitas para autorizar logins e utilizadores a executar ações e dados de consulta.
keywords: segurança de base de dados sql,gestão de segurança da base de dados,segurança de início de sessão,segurança de base de dados,acesso de base de dados
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 0f1611e6d3524cc78fc20fed9d1aac6f3fd453fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106445"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Autorizar o acesso à base de dados a utilizadores autenticados na Base de Dados SQL e na Azure Synapse Analytics utilizando logins e contas de utilizador

Neste artigo, aprende-se sobre:

- Opções para configurar a Base de Dados Azure SQL e a Azure Synapse Analytics (antiga Azure SQL Data Warehouse) para permitir aos utilizadores executar tarefas administrativas e aceder aos dados armazenados nestas bases de dados.
- A configuração de acesso e autorização após a criação inicial de uma nova Base de Dados Azure SQL
- Como adicionar logins e contas de utilizador na base de dados principal e contas de utilizador e, em seguida, conceder permissões administrativas a essas contas
- Como adicionar contas de utilizador nas bases de dados dos utilizadores, associadas a logins ou como contas de utilizador contidas
- Configure as contas dos utilizadores com permissões nas bases de dados dos utilizadores utilizando funções de base de dados e permissões explícitas

> [!IMPORTANT]
> As bases de dados da Base de Dados Azure SQL e da Azure Synapse Analytics (antiga Azure SQL Data Warehouse) são referidas colectivamente no restante deste artigo como bases de dados ou como Azure SQL (para simplicidade).

## <a name="authentication-and-authorization"></a>Autenticação e autorização

[**A autenticação**](sql-database-security-overview.md#authentication) é o processo de provar que o utilizador é quem afirmam ser. Um utilizador liga-se a uma base de dados utilizando uma conta de utilizador.
Quando um utilizador tenta ligar-se a uma base de dados, fornece uma conta de utilizador e informações de autenticação. O utilizador é autenticado utilizando um dos dois seguintes métodos de autenticação:

- [Autenticação SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Com este método de autenticação, o utilizador submete um nome de conta de utilizador e uma senha associada para estabelecer uma ligação. Esta palavra-passe é armazenada na base de dados principal para contas de utilizador ligadas a um login ou armazenadas na base de dados contendo as contas do utilizador *não* ligadas a um login.
- [Autenticação do Azure Active Directory](sql-database-aad-authentication.md)

  Com este método de autenticação, o utilizador submete um nome de conta de utilizador e solicita que o serviço utilize as informações credenciais armazenadas no Diretório Ativo Azure.

**Logins e utilizadores**: No Azure SQL, uma conta de utilizador numa base de dados pode ser associada a um login que é armazenado na base de dados principal ou pode ser um nome de utilizador que é armazenado numa base de dados individual.

- Um **login** é uma conta individual na base de dados principal, à qual uma conta de utilizador numa ou mais bases de dados pode ser ligada. Com um login, as informações de credenciais para a conta de utilizador são armazenadas com o login.
- Uma **conta de utilizador** é uma conta individual em qualquer base de dados que possa ser, mas não tem de estar ligada a um login. Com uma conta de utilizador que não esteja ligada a um login, a informação de credencial é armazenada na conta do utilizador.

[**A autorização**](sql-database-security-overview.md#authorization) para aceder a dados e executar várias ações é gerida usando funções de base de dados e permissões explícitas. A autorização refere-se às permissões atribuídas a um utilizador e determina o que esse utilizador pode fazer. A autorização é controlada pelas [subscrições](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) de funções de base de dados da sua conta de utilizador e [permissões ao nível do objeto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logins existentes e contas de utilizador após a criação de uma nova base de dados

Quando criar a sua primeira implementação Azure SQL, especifice um login de administrador e uma palavra-passe associada para esse login. Esta conta administrativa chama-se **Administração do Servidor**. A seguinte configuração de logins e utilizadores nas bases de dados master e utilizador ocorre durante a implementação:

- É criado um login SQL com privilégios administrativos utilizando o nome de login que especificou. Um [login](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta individual de utilizador para iniciar sessão na Base de Dados SQL.
- Este login é concedido permissões administrativas completas em todas as bases de dados como [um diretor de nível de servidor](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Este login tem todas as permissões disponíveis dentro da Base de Dados SQL e não pode ser limitado. Num caso gerido, este login é adicionado à função de [servidor fixo sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (esta função não existe com bases de dados únicas ou agrupadas).
- É criada `dbo` uma conta de [utilizador](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) chamada para este login em cada base de dados do utilizador. O utilizador [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de `db_owner` base de dados na base de dados e está mapeado para a função de base de dados fixa. Funções adicionais de base de dados fixas são discutidas mais tarde neste artigo.

Para identificar as contas do administrador de uma base de dados, abra o portal Azure e navegue para o separador **Propriedades** do seu servidor ou instância gerida.

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins.png)

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> O nome de login administrativo não pode ser alterado depois de ter sido criado. Para redefinir a palavra-passe para o administrador lógico do servidor, aceda ao [portal Azure,](https://portal.azure.com)clique em **Servidores SQL,** selecione o servidor a partir da lista e, em seguida, clique em **Redefinir palavra-passe**. Para redefinir a palavra-passe para um servidor de instância gerido, vá ao portal Azure, clique na instância e clique em Redefinir a **palavra-passe**. Também pode utilizar o PowerShell ou o Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Criar logins adicionais e utilizadores com permissões administrativas

Neste ponto, a sua instância Azure SQL só está configurada para acesso através de uma única conta de login E utilizador SQL. Para criar logins adicionais com permissões administrativas completas ou parciais, tem as seguintes opções (dependendo do modo de implementação):

- **Criar uma conta de administrador de Diretório Ativo Azure com permissões administrativas completas**

  Ativar a autenticação do Diretório Ativo Azure e criar um login de administrador da AD Azure. Uma conta azure Ative Diretório pode ser configurada como administradora da implementação da Base de Dados SQL com permissões administrativas completas. Esta conta pode ser uma conta individual ou de grupo de segurança. Um administrador da AD Azure **deve** ser configurado se pretender utilizar contas AD Azure para ligar à Base de Dados SQL. Para obter informações detalhadas sobre a autenticação da AD Azure para todos os tipos de implementação da Base de Dados SQL, consulte os seguintes artigos:

  - [Utilize autenticação de diretório ativo Azure para autenticação com SQL](sql-database-aad-authentication.md)
  - [Configure e gerea autenticação de Diretório Ativo Azure com SQL](sql-database-aad-authentication-configure.md)

- **Numa implementação de instância gerida, crie logins SQL com permissões administrativas completas**

  - Criar um login adicional do SQL Server na instância gerida
  - Adicione o login à função de [servidor fixo sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) usando a declaração [DE FUNÇÃO DO SERVIDOR ALTER.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Este login terá permissões administrativas completas.
  - Em alternativa, [crie um login Azure AD](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) utilizando a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN.</a>

- **Numa implantação única ou agrupada, crie logins SQL com permissões administrativas limitadas**

  - Crie um login SQL adicional na base de dados principal para uma única ou agrupada implantação de bases de dados, ou uma implementação de instância gerida
  - Crie uma conta de utilizador na base de dados principal associada a este novo login
  - Adicione a conta `dbmanager`de `loginmanager` utilizador à função `master` , ou ambas na base de dados utilizando a declaração [DE FUNÇÃO ALTER SERVER](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (para Anapse Analytics Azure, utilize a declaração [sp_addrolemember).](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`e `loginmanager` as funções **não** dizem respeito a implementações de instâncias geridas.

  Os membros destas [funções especiais](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) de base de dados para bases de dados individuais ou reunidas permitem que os utilizadores tenham autoridade para criar e gerir bases de dados ou para criar e gerir logins. Nas bases de dados criadas por `dbmanager` um utilizador que é membro `db_owner` da função, o membro é mapeado para a função de base de dados fixa e pode iniciar sessão e gerir essa base de dados utilizando a conta de `dbo` utilizador. Estas funções não têm permissões explícitas fora da base de dados principal.

  > [!IMPORTANT]
  > Não é possível criar um login SQL adicional com permissões administrativas completas numa única base de dados ou agrupada.

## <a name="create-accounts-for-non-administrator-users"></a>Criar contas para utilizadores não administradores

Pode criar contas para utilizadores não administrativos utilizando um de dois métodos:

- **Criar um login**

  Crie um login SQL na base de dados principal. Em seguida, crie uma conta de utilizador em cada base de dados a que esse utilizador precisa de aceder e associe a conta de utilizador a esse login. Esta abordagem é preferível quando o utilizador deve aceder a várias bases de dados e pretende manter as palavras-passe sincronizadas. No entanto, esta abordagem tem complexidades quando utilizada com geo-replicação, uma vez que o login deve ser criado tanto no servidor primário como no servidor secundário. Para mais informações, consulte configure e gerencie a segurança da Base de [Dados Azure SQL para geo-restaurar ou falhar](sql-database-geo-replication-security-config.md).
- **Criar uma conta de utilizador**

  Crie uma conta de utilizador na base de dados a que um utilizador precisa de acesso (também chamado [utilizador contido).](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Com uma base de dados única ou agrofada, pode sempre criar este tipo de conta de utilizador.
  - Com uma base de dados de instância gerida que não suporta os diretores de [servidores da AD Azure,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)só pode criar este tipo de conta de utilizador numa base de [dados contida](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Com a instância gerida que suporta os diretores de [servidores da AD Azure,](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)pode criar contas de utilizador para autenticar a instância gerida sem exigir que os utilizadores de bases de dados sejam criados como um utilizador de base de dados contido.

  Com esta abordagem, as informações de autenticação do utilizador são armazenadas em cada base de dados e replicadas automaticamente em bases de dados geo-replicadas. No entanto, se a mesma conta existir em várias bases de dados e estiver a utilizar a Autenticação SQL, deve manter as palavras-passe sincronizadas manualmente. Além disso, se um utilizador tiver uma conta em diferentes bases de dados com senhas diferentes, lembrar essas palavras-passe pode tornar-se um problema.

> [!IMPORTANT]
> Para criar utilizadores contidos mapeados para identidades AD Azure, você deve ser registrado na utilização de uma conta Azure AD que é um administrador na Base de Dados SQL. Em caso gerido, um login `sysadmin` SQL com permissões também pode criar um login ou utilizador azure AD.

Por exemplo, mostrando como criar logins e utilizadores, consulte:

- [Criar login para bases de dados individuais ou agrupadas](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Criar login para base de dados de instâncias geridas](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Crie login para base de dados Azure Synapse Analytics](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Criar utilizador](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Criar anúncio sinuoso continha utilizadores](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> Para um tutorial de segurança que inclua a criação de um Servidor SQL um utilizador contido numa única base de dados ou agrinado, consulte [Tutorial: Proteja uma única ou piscina](sql-database-security-tutorial.md)de bases de dados .

## <a name="using-fixed-and-custom-database-roles"></a>Utilização de funções de base de dados fixas e personalizadas

Depois de criar uma conta de utilizador numa base de dados, quer com base num login quer como utilizador contido, pode autorizar esse utilizador a executar várias ações e a aceder a dados numa determinada base de dados. Pode utilizar os seguintes métodos para autorizar o acesso:

- **Funções fixas de base de dados**

  Adicione a conta de utilizador a uma [função](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)de base de dados fixa . Existem 9 funções fixas de base de dados, cada uma com um conjunto definido de permissões. As funções de base de dados fixas mais comuns são: **db_owner,** **db_ddladmin,** **db_datawriter,** **db_datareader,** **db_denydatawriter**e **db_denydatareader.** **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. Por exemplo, o **db_datareader** papel de base de dados fixo slera o acesso a cada quadro da base de dados, o que é mais do que o necessário.

  - Para adicionar um utilizador a uma função de base de dados fixa:

    - Na Base de Dados Azure SQL, utilize a declaração [ALTER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) Por exemplo, consulte [exemplos ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics, use a [declaração sp_addrolemember.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) Por exemplo, consulte [sp_addrolemember exemplos.](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)

- **Papel de base de dados personalizada**

  Crie uma função de base de dados personalizada utilizando a declaração [CREATE ROLE.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Uma função personalizada permite-lhe criar as suas próprias funções de base de dados definidas pelo utilizador e conceder cuidadosamente a cada função as menos permissões necessárias para a necessidade do negócio. Em seguida, pode adicionar os utilizadores à função personalizada. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.
- **Conceder permissões diretamente**

  Conceda diretamente [as permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) da conta de utilizador. Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e reveja o [gráfico em tamanho de cartaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Usando grupos

A gestão eficiente do acesso utiliza permissões atribuídas a grupos de segurança de Diretório Ativo e funções fixas ou personalizadas em vez de utilizadores individuais.

- Ao utilizar a autenticação do Diretório Ativo Azure, coloque os utilizadores do Azure Ative Directory num grupo de segurança do Diretório Ativo Azure. Crie um utilizador de base de dados contido para o grupo. Coloque um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

- Ao utilizar a autenticação SQL, crie utilizadores de base de dados contidos na base de dados. Coloque um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

  > [!NOTE]
  > Também pode utilizar grupos para utilizadores de bases de dados não contidos.

Deverá familiarizar-se com as seguintes funcionalidades que podem ser utilizadas para limitar ou efetuar a elevação de permissões:

- A [representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e a [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser utilizadas para elevar temporariamente permissões de forma segura.
- A [Segurança ao Nível da Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser utilizada para limitar as linhas a que um utilizador pode aceder.
- A [Máscara de Dados](sql-database-dynamic-data-masking-get-started.md) pode ser utilizada para limitar a exposição de dados confidenciais.
- Os [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral de todas as funcionalidades de segurança da Base de Dados SQL, veja a [Descrição geral da segurança de SQL](sql-database-security-overview.md).
