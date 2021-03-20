---
title: Configure a segurança para a recuperação de desastres
description: Aprenda as considerações de segurança para configurar e gerir a segurança após a restauração de uma base de dados ou uma falha num servidor secundário.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 317b530fbaa34ca5689bb505126892e4eba06bd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674806"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configure e gere a segurança da Base de Dados Azure SQL para a geo-restauração ou falha
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve os requisitos de autenticação para configurar e controlar [grupos](auto-failover-group-overview.md) [ativos de geo-replicação](active-geo-replication-overview.md) e de falha automática . Também fornece as etapas necessárias para configurar o acesso do utilizador à base de dados secundária. Por último, também descreve como permitir o acesso à base de dados recuperada após a utilização [do geo-restauro.](recovery-using-backups.md#geo-restore) Para obter mais informações sobre as opções de recuperação, consulte [a Visão Geral da Continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="disaster-recovery-with-contained-users"></a>Recuperação de desastres com utilizadores contidos

Ao contrário dos utilizadores tradicionais, que devem ser mapeados para logins na base de dados principal, um utilizador contido é gerido completamente pela própria base de dados. Isto tem dois benefícios. No cenário de recuperação de desastres, os utilizadores podem continuar a ligar-se à nova base de dados primária ou à base de dados recuperada utilizando o geo-restauro sem qualquer configuração adicional, porque a base de dados gere os utilizadores. Existem também potenciais benefícios de escalabilidade e desempenho a partir desta configuração a partir de uma perspetiva de login. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos – Tornar a Sua Base de Dados Portátil)](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

A principal compensação é que gerir o processo de recuperação de desastres em escala é mais desafiante. Quando tiver várias bases de dados que utilizam o mesmo login, manter as credenciais que utilizam utilizadores contidos em várias bases de dados pode anular os benefícios dos utilizadores contidos. Por exemplo, a política de rotação de palavras-passe requer que as alterações sejam feitas de forma consistente em várias bases de dados em vez de alterar a palavra-passe para o login uma vez na base de dados principal. Por esta razão, se tiver várias bases de dados que utilizem o mesmo nome de utilizador e palavra-passe, não é recomendável utilizar utilizadores contidos.

## <a name="how-to-configure-logins-and-users"></a>Como configurar logins e utilizadores

Se estiver a utilizar logins e utilizadores (em vez de utilizadores contidos), tem de tomar medidas adicionais para garantir que os mesmos logins existem na base de dados principal. As secções seguintes descrevem as etapas envolvidas e considerações adicionais.

  >[!NOTE]
  > Também é possível utilizar logins do Azure Ative Directory (AAD) para gerir as suas bases de dados. Para mais informações, consulte [os logins e utilizadores do Azure SQL.](./logins-create-manage.md)

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso do utilizador a uma base de dados secundária ou recuperada

Para que a base de dados secundária seja utilizável como base de dados secundária apenas de leitura e para garantir o acesso adequado à nova base de dados primária ou à base de dados recuperada utilizando o geo-restauro, a base de dados principal do servidor alvo deve ter a configuração de segurança adequada antes da recuperação.

As permissões específicas para cada passo são descritas mais tarde neste tópico.

A preparação do acesso do utilizador a uma geo-replicação secundária deve ser executada como configuração de geo-replicação. A preparação do acesso do utilizador às bases de dados geo-restauradas deve ser efetuada a qualquer momento quando o servidor original estiver online (por exemplo, como parte da broca DR).

> [!NOTE]
> Se falhar ou restaurar geo-restauro a um servidor que não tenha logins devidamente configurados, o acesso ao mesmo será limitado à conta de administração do servidor.

A configuração de logins no servidor alvo envolve três passos descritos abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determinar os logins com acesso à base de dados primária

O primeiro passo do processo é determinar quais os logins que devem ser duplicados no servidor alvo. Isto é realizado com um par de declarações SELECT, uma na base de dados lógica principal no servidor de origem e outra na própria base de dados primária.

Apenas o administrador do servidor ou um membro da função do servidor **LoginManager** podem determinar os logins no servidor de origem com a seguinte declaração SELECT.

```sql
SELECT [name], [sid]
FROM [sys].[sql_logins]
WHERE [type_desc] = 'SQL_Login'
```

Apenas um membro da função de base de dados db_owner, o utilizador dbo, ou administrador do servidor, pode determinar todos os principais utilizadores da base de dados na base de dados primária.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Encontre o SID para os logins identificados no passo 1

Ao comparar a saída das consultas da secção anterior e a correspondência dos SIDs, pode mapear o login do servidor para o utilizador da base de dados. Os logins que têm um utilizador de base de dados com um SID correspondente têm acesso ao utilizador nessa base de dados como o principal dos utilizadores da base de dados.

A seguinte consulta pode ser usada para ver todos os principais utilizadores e seus SIDs numa base de dados. Apenas um membro da db_owner função de base de dados ou administrador de servidor pode executar esta consulta.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

> [!NOTE]
> Os **utilizadores de INFORMATION_SCHEMA** e **sys** têm *SIDs NULOS,* e o **SID convidado** é **0x00**. O **dbo** SID pode começar com *0x01060000000001648000000000048454,* se o criador da base de dados fosse o administrador do servidor em vez de um membro da **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Criar os logins no servidor alvo

O último passo é ir para o servidor alvo, ou servidores, e gerar os logins com os SIDs apropriados. A sintaxe básica é a seguinte.

```sql
CREATE LOGIN [<login name>]
WITH PASSWORD = <login password>,
SID = <desired login SID>
```

> [!NOTE]
> Se pretender conceder o acesso do utilizador ao secundário, mas não ao primário, pode fazê-lo alterando o login do utilizador no servidor primário utilizando a seguinte sintaxe.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Desativar não altera a palavra-passe, pelo que pode sempre ative-la se necessário.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a gestão do acesso à base de dados e dos logins, consulte [a segurança da Base de Dados SQL: Gerir o acesso à base de dados e a segurança de login.](logins-create-manage.md)
* Para obter mais informações sobre utilizadores de bases de dados contidos, consulte [Utilizadores de bases de dados contidos - Tornando a sua base de dados portátil](/sql/relational-databases/security/contained-database-users-making-your-database-portable).
* Para saber sobre a geo-replicação ativa, consulte [a geo-replicação ativa.](active-geo-replication-overview.md)
* Para conhecer os grupos de falhas automáticas, consulte [os grupos auto-failover](auto-failover-group-overview.md).
* Para obter informações sobre a utilização de geo-restauro, consulte [geo-restauro](recovery-using-backups.md#geo-restore)