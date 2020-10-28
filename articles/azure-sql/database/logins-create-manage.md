---
title: Autorizar o acesso ao servidor e à base de dados utilizando logins e contas de utilizador
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como a Azure SQL Database, SQL Managed Instance e Azure Synapse autenticam os utilizadores para acesso através de logins e contas de utilizador. Saiba também como conceder funções de base de dados e permissões explícitas para autorizar logins e utilizadores a realizar ações e dados de consulta.
keywords: segurança de base de dados sql,gestão de segurança da base de dados,segurança de início de sessão,segurança de base de dados,acesso de base de dados
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: 940ea0ac471604b22c64dc008eebd8b580121cf7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782744"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autorizar o acesso à base de dados SQL, à SqL Managed Instance e ao Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste artigo, aprende-se:

- Opções para configurar a Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics (anteriormente SQL Data Warehouse) para permitir aos utilizadores executar tarefas administrativas e aceder aos dados armazenados nestas bases de dados.
- A configuração de acesso e autorização após a criação inicial de um novo servidor.
- Como adicionar logins e contas de utilizador na base de dados principal e contas de utilizador e, em seguida, conceder essas permissões administrativas.
- Como adicionar contas de utilizador em bases de dados de utilizadores, quer associadas a logins, quer como contas de utilizador contidas.
- Configure as contas dos utilizadores com permissões nas bases de dados dos utilizadores utilizando funções de base de dados e permissões explícitas.

> [!IMPORTANT]
> As bases de dados em Azure SQL Database, Azure SQL Managed Instance e Azure Synapse são referidas coletivamente no restante deste artigo como bases de dados, e o servidor refere-se ao [servidor](logical-servers.md) que gere bases de dados para Azure SQL Database e Azure Synapse.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

[**A autenticação**](security-overview.md#authentication) é o processo de provar que o utilizador é quem diz ser. Um utilizador liga-se a uma base de dados utilizando uma conta de utilizador.
Quando um utilizador tenta ligar-se a uma base de dados, fornece uma conta de utilizador e informações de autenticação. O utilizador é autenticado utilizando um dos dois seguintes métodos de autenticação:

- [Autenticação SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Com este método de autenticação, o utilizador submete um nome de conta de utilizador e senha associada para estabelecer uma ligação. Esta palavra-passe é armazenada na base de dados principal para contas de utilizador ligadas a um login ou armazenadas na base de dados que contém as contas de utilizador *não* ligadas a um login.
- [Autenticação do Azure Active Directory](authentication-aad-overview.md)

  Com este método de autenticação, o utilizador submete um nome de conta de utilizador e solicita que o serviço utilize as informações credenciais armazenadas no Diretório Azure Ative (Azure AD).

**Logins e utilizadores** : Uma conta de utilizador numa base de dados pode ser associada a um login que é armazenado na base de dados principal ou pode ser um nome de utilizador que é armazenado numa base de dados individual.

- Um **login** é uma conta individual na base de dados principal, à qual uma conta de utilizador numa ou mais bases de dados pode ser ligada. Com o início de sessão, as informações das credenciais da conta de utilizador são armazenadas no início de sessão.
- Uma **conta de utilizador** é uma conta individual em qualquer base de dados que possa estar, mas não tem de estar, ligada a um login. Com uma conta de utilizador não associada a um início de sessão, as informações das credenciais são armazenadas na conta de utilizador.

[**A autorização**](security-overview.md#authorization) para aceder aos dados e realizar várias ações é gerida usando funções de base de dados e permissões explícitas. A autorização refere-se às permissões atribuídas a um utilizador e determina o que esse utilizador pode fazer. A autorização é controlada pelos [membros](/sql/relational-databases/security/authentication-access/database-level-roles) da função de base de dados da sua conta de utilizador e [permissões de nível de objetos.](/sql/relational-databases/security/permissions-database-engine) Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Logins e contas de utilizador existentes após a criação de uma nova base de dados

Quando implementar pela primeira vez o Azure SQL, especifique um login de administração e uma palavra-passe associada para esse login. Esta conta administrativa chama-se **Administrador do Servidor.** A seguinte configuração de logins e utilizadores nas bases de dados principal e do utilizador ocorre durante a implementação:

- Um login SQL com privilégios administrativos é criado usando o nome de login especificado. Um [login](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) é uma conta de utilizador individual para iniciar sessão na BASE de Dados SQL, SQL Managed Instance e Azure Synapse.
- Este login tem permissões administrativas completas em todas as bases de dados como [principal de nível de servidor](/sql/relational-databases/security/authentication-access/principals-database-engine). O login tem todas as permissões disponíveis e não pode ser limitado. Numa SQL Managed Instance, este login é adicionado à [função de servidor fixo sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) (esta função não existe na Base de Dados Azure SQL).
- Uma [conta de utilizador](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) chamada para este login em cada base de `dbo` dados de utilizadores. O utilizador [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) tem todas as permissões de base de dados na base de dados e está mapeado para a `db_owner` função de base de dados fixa. As funções de base de dados fixas adicionais são discutidas mais tarde neste artigo.

Para identificar as contas do administrador de uma base de dados, abra o portal Azure e navegue no **separador Propriedades** do seu servidor ou caso gerido.

![Administradores do SQL Server](./media/logins-create-manage/sql-admins.png)

![Screenshot que realça a opção do menu Propriedades.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> O nome de login não pode ser alterado depois de ter sido criado. Para redefinir a palavra-passe para a administração do servidor, aceda ao [portal Azure](https://portal.azure.com), clique em **SQL Servers** , selecione o servidor da lista e, em seguida, clique em **Redefinir a Palavra-passe** . Para redefinir a palavra-passe para a SqL Managed Instance, vá ao portal Azure, clique na instância e clique na **palavra-passe Reset** . Também pode utilizar o PowerShell ou o Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Criar logins adicionais e utilizadores com permissões administrativas

Neste momento, o seu servidor ou instância gerida só está configurado para acesso utilizando um único login SQL e conta de utilizador. Para criar logins adicionais com permissões administrativas completas ou parciais, tem as seguintes opções (dependendo do seu modo de implantação):

- **Criar uma conta de administrador Azure Ative Directory com permissões administrativas completas**

  Ativar a autenticação do Azure Ative Directory e criar um login de administrador AD Azure. Uma conta do Azure Ative Directory pode ser configurada como administradora da implantação do Azure SQL com permissões administrativas completas. Esta conta pode ser uma conta individual ou de grupo de segurança. Um administrador AD Azure **deve** ser configurado se quiser utilizar contas AZURE AD para ligar à Base de Dados SQL, SQL Managed Instance ou Azure Synapse. Para obter informações detalhadas sobre a autenticação Azure AD para todos os tipos de implantação Azure SQL, consulte os seguintes artigos:

  - [Utilize autenticação do Diretório Ativo Azure para autenticação com SQL](authentication-aad-overview.md)
  - [Configure e gere a autenticação do Azure Ative Directory com o SQL](authentication-aad-configure.md)

- **Em SQL Managed Instance, crie logins SQL com permissões administrativas completas**

  - Crie um login SQL adicional na base de dados principal.
  - Adicione o login à [função de servidor fixo sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) utilizando a declaração [ALTER SERVER ROLE.](/sql/t-sql/statements/alter-server-role-transact-sql) Este login terá permissões administrativas completas.
  - Em alternativa, crie um [login AD Azure](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) utilizando a sintaxe [DE LOGIN CREATE.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)

- **Na Base de Dados SQL, crie logins SQL com permissões administrativas limitadas**

  - Crie um login SQL adicional na base de dados principal.
  - Crie uma conta de utilizador na base de dados principal associada a este novo login.
  - Adicione a conta de utilizador à `dbmanager` , `loginmanager` função, ou ambos na `master` base de dados utilizando a declaração ALTER [ROLE](/sql/t-sql/statements/alter-role-transact-sql) (para Azure Synapse, utilize a declaração [sp_addrolemember).](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager` e `loginmanager` as funções **não** dizem respeito a implementações de instâncias geridas SQL.

  Os membros destas [funções de base de dados-chave para](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) a Base de Dados Azure SQL têm autoridade para criar e gerir bases de dados ou para criar e gerir logins. Nas bases de dados criadas por um utilizador que é membro da `dbmanager` função, o membro está mapeado para a `db_owner` função de base de dados fixa e pode iniciar sessão e gerir essa base de dados utilizando a conta de `dbo` utilizador. Estas funções não têm permissões explícitas fora da base de dados principal.

  > [!IMPORTANT]
  > Não é possível criar um login SQL adicional com permissões administrativas completas na Base de Dados SQL.

## <a name="create-accounts-for-non-administrator-users"></a>Criar contas para utilizadores não administradores

Pode criar contas para utilizadores não administrativos utilizando um de dois métodos:

- **Criar um login**

  Crie um login SQL na base de dados principal. Em seguida, crie uma conta de utilizador em cada base de dados à qual esse utilizador precise de acesso e associe a conta de utilizador a esse login. Esta abordagem é preferida quando o utilizador tem de aceder a várias bases de dados e deseja manter as palavras-passe sincronizadas. No entanto, esta abordagem tem complexidades quando usada com geo-replicação, uma vez que o login deve ser criado tanto no servidor primário como nos servidores secundários. Para obter mais informações, consulte [a Configuração e gerencie a segurança da Base de Dados Azure SQL para geo-restauro ou falha.](active-geo-replication-security-configure.md)
- **Criar uma conta de utilizador**

  Criar uma conta de utilizador na base de dados à qual um utilizador precisa de acesso (também chamado de [utilizador contido).](/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Com a BASE de Dados SQL, pode sempre criar este tipo de conta de utilizador.
  - Com a SQL Managed Instance a suportar [os principais servidores Azure AD,](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)pode criar contas de utilizador para autenticar para a SQL Managed Instance sem exigir que os utilizadores da base de dados sejam criados como um utilizador de base de dados contido.

  Com esta abordagem, a informação de autenticação do utilizador é armazenada em cada base de dados e replicada automaticamente em bases de dados geo-replicadas. No entanto, se a mesma conta existir em várias bases de dados e estiver a utilizar a Autenticação Azure SQL, deve manter as palavras-passe sincronizadas manualmente. Além disso, se um utilizador tiver uma conta em diferentes bases de dados com senhas diferentes, lembrar essas palavras-passe pode tornar-se um problema.

> [!IMPORTANT]
> Para criar utilizadores contidos mapeados para identidades Azure AD, você deve ser iniciado em usando uma conta AD Azure que é um administrador na base de dados em Azure SQL Database. Em SQL Managed Instance, um login SQL com `sysadmin` permissões também pode criar um login AD Azure ou utilizador.

Para exemplos que mostram como criar logins e utilizadores, consulte:

- [Criar login para Azure SQL Database](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Criar login para Azure SQL Managed Instance](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Criar login para Azure Synapse](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Criar utilizador](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Criar utilizadores Azure AD contidos](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Para um tutorial de segurança que inclua a criação de utilizadores na Base de Dados Azure [SQL, consulte Tutorial: Secure Azure SQL Database](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Utilização de funções de base de dados fixas e personalizadas

Depois de criar uma conta de utilizador numa base de dados, quer com base num login, quer como utilizador contido, pode autorizar esse utilizador a realizar várias ações e a aceder a dados numa determinada base de dados. Pode utilizar os seguintes métodos para autorizar o acesso:

- **Funções de base de dados fixas**

  Adicione a conta de utilizador a uma [função de base de dados fixa.](/sql/relational-databases/security/authentication-access/database-level-roles) Existem 9 funções fixas de base de dados, cada uma com um conjunto definido de permissões. As funções de base de dados fixas mais comuns são: **db_owner,** **db_ddladmin,** **db_datawriter,** **db_datareader,** **db_denydatawriter** e **db_denydatareader.** **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. Por exemplo, a **db_datareader** papel fixo da base de dados concede o acesso a todas as tabelas da base de dados, o que é mais do que o estritamente necessário.

  - Para adicionar um utilizador a uma função de base de dados fixa:

    - Na Base de Dados Azure SQL, utilize a declaração [ALTER ROLE.](/sql/t-sql/statements/alter-role-transact-sql) Por exemplo, consulte [exemplos DE ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse, use a [declaração sp_addrolemember.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) Por exemplo, consulte [sp_addrolemember exemplos.](/sql/t-sql/statements/alter-role-transact-sql)

- **Papel de base de dados personalizada**

  Crie uma função de base de dados personalizada utilizando a declaração [CREATE ROLE.](/sql/t-sql/statements/create-role-transact-sql) Uma função personalizada permite-lhe criar as suas próprias funções de base de dados definidas pelo utilizador e conceder cuidadosamente a cada papel as menos permissões necessárias para a necessidade do negócio. Em seguida, pode adicionar os utilizadores ao papel personalizado. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.
- **Conceder permissões diretamente**

  Conceda diretamente as [permissões](/sql/relational-databases/security/permissions-database-engine) da conta de utilizador. Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](/sql/relational-databases/security/permissions-database-engine) e reveja o [gráfico em tamanho de cartaz](/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

## <a name="using-groups"></a>Usando grupos

Uma gestão eficiente do acesso utiliza permissões atribuídas a grupos de segurança do Ative Directory e funções fixas ou personalizadas em vez de utilizadores individuais.

- Ao utilizar a autenticação do Azure Ative Directory, coloque os utilizadores do Azure Ative Directory num grupo de segurança do Azure Ative Directory. Crie um utilizador de base de dados contido para o grupo. Adicione um ou mais utilizadores de bases de dados como membros para personalizar ou construir funções de base de dados com as permissões específicas adequadas a esse grupo de utilizadores.

- Ao utilizar a autenticação SQL, crie utilizadores de bases de dados contidos na base de dados. Coloque um ou mais utilizadores de bases de dados numa função de base de dados personalizada com permissões específicas adequadas a esse grupo de utilizadores.

  > [!NOTE]
  > Também pode utilizar grupos para utilizadores de bases de dados não contidos.

Deverá familiarizar-se com as seguintes funcionalidades que podem ser utilizadas para limitar ou efetuar a elevação de permissões:

- A [representação](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e a [assinatura de módulo](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser utilizadas para elevar temporariamente permissões de forma segura.
- A [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security) pode ser utilizada para limitar as linhas a que um utilizador pode aceder.
- A [Máscara de Dados](dynamic-data-masking-overview.md) pode ser utilizada para limitar a exposição de dados confidenciais.
- Os [Procedimentos armazenados](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral de todas as funcionalidades de segurança da Azure SQL Database e sql Managed Instance, consulte [a visão geral de Segurança](security-overview.md).