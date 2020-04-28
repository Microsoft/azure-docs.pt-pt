---
title: Configurar a segurança para a recuperação de desastres
description: Aprenda as considerações de segurança para configurar e gerir a segurança após a restauração de uma base de dados ou uma falha num servidor secundário.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73807487"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configure e gerencie a segurança da Base de dados Azure SQL para geo-restauro ou falha

Este artigo descreve os requisitos de autenticação para configurar e controlar grupos [ativos de geo-replicação](sql-database-active-geo-replication.md) e [falha automática](sql-database-auto-failover-group.md). Fornece também as etapas necessárias para configurar o acesso dos utilizadores à base de dados secundária. Por último, descreve também como permitir o acesso à base de dados recuperada após a [utilização do geo-restauro.](sql-database-recovery-using-backups.md#geo-restore) Para obter mais informações sobre as opções de recuperação, consulte a [visão geral da continuidade do negócio.](sql-database-business-continuity.md)

## <a name="disaster-recovery-with-contained-users"></a>Recuperação de desastres com utilizadores contidos

Ao contrário dos utilizadores tradicionais, que devem ser mapeados para logins na base de dados principal, um utilizador contido é gerido completamente pela própria base de dados. Isto tem dois benefícios. No cenário de recuperação de desastres, os utilizadores podem continuar a ligar-se à nova base de dados primária ou à base de dados recuperada utilizando geo-restauro sem qualquer configuração adicional, uma vez que a base de dados gere os utilizadores. Há também potenciais benefícios de escalabilidade e desempenho desta configuração a partir de uma perspetiva de login. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).

A principal compensação é que gerir o processo de recuperação de desastres em escala é mais desafiante. Quando tem várias bases de dados que utilizam o mesmo login, manter as credenciais utilizando utilizadores contidos em várias bases de dados pode anular os benefícios dos utilizadores contidos. Por exemplo, a política de rotação de palavras-passe requer que as alterações sejam feitas de forma consistente em várias bases de dados, em vez de alterar a palavra-passe para o login uma vez na base de dados principal. Por esta razão, se tiver várias bases de dados que utilizem o mesmo nome de utilizador e palavra-passe, não é recomendado utilizar utilizadores contidos.

## <a name="how-to-configure-logins-and-users"></a>Como configurar logins e utilizadores

Se estiver a utilizar logins e utilizadores (em vez de utilizadores contidos), deve tomar medidas adicionais para garantir que os mesmos logins existem na base de dados principal. As secções seguintes descrevem os passos envolvidos e considerações adicionais.

  >[!NOTE]
  > Também é possível utilizar logins de Diretório Ativo Azure (AAD) para gerir as suas bases de dados. Para mais informações, consulte os [logins e utilizadores do Azure SQL.](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso do utilizador a uma base de dados secundária ou recuperada

Para que a base de dados secundária seja utilizável como base de dados secundária para leitura e para garantir o acesso adequado à nova base de dados primária ou à base de dados recuperada utilizando geo-restauro, a base de dados principal do servidor-alvo deve ter a configuração de segurança adequada no lugar antes da recuperação.

As permissões específicas para cada passo são descritas mais tarde neste tópico.

A preparação do acesso do utilizador a um secundário de geo-replicação deve ser realizada como parte da configuração da geo-replicação. A preparação do acesso do utilizador às bases de dados georestauradas deve ser realizada a qualquer momento em que o servidor original esteja on-line (por exemplo, como parte da broca DR).

> [!NOTE]
> Se falhar ou geo-restaurar a um servidor que não tenha logins devidamente configurados, o acesso ao mesmo será limitado à conta de administração do servidor.

A configuração de logins no servidor alvo envolve três passos descritos abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determinar os logins com acesso à base de dados primária

O primeiro passo do processo é determinar quais os logins que devem ser duplicados no servidor alvo. Isto é realizado com um par de declarações SELECT, uma na base de dados lógica do servidor de origem e outra na própria base de dados primária.

Apenas o administrador do servidor ou um membro da função do servidor **LoginManager** podem determinar os logins no servidor de origem com a seguinte declaração SELECT.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Apenas um membro da função de base de dados db_owner, o utilizador dbo, ou administração do servidor, pode determinar todos os diretores de utilizadores da base de dados na base de dados primária.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Encontre o SID para os logins identificados no passo 1

Comparando a saída das consultas da secção anterior e combinando com os SIDs, pode mapear o login do servidor com o utilizador da base de dados. Os logins que têm um utilizador de base de dados com um SID correspondente têm acesso ao utilizador a essa base de dados como principal utilizador da base de dados.

A seguinte consulta pode ser usada para ver todos os diretores do utilizador e seus SIDs numa base de dados. Apenas um membro da função de base de dados db_owner ou administrador de servidor pode executar esta consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Os **utilizadores INFORMATION_SCHEMA** e **sys** têm SIDs *NULOs,* e o SID **convidado** é **0x00**. O **dbo** SID pode começar com *0x010600000000000168000000000000000000048454*, se o criador da base de dados fosse o administrador do servidor em vez de um membro do **DbManager.**

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Criar os logins no servidor alvo

O último passo é ir ao servidor alvo, ou servidores, e gerar os logins com os SIDs apropriados. A sintaxe básica é a seguinte.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Se pretender conceder ao utilizador acesso ao secundário, mas não ao primário, pode fazê-lo alterando o login do utilizador no servidor principal utilizando a seguinte sintaxe.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> O Desative não altera a palavra-passe, pelo que pode sempre ativar se necessário.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a gestão do acesso à base de dados e dos logins, consulte a segurança da Base de [Dados SQL: Gerir o acesso](sql-database-manage-logins.md)à base de dados e a segurança de login .
* Para obter mais informações sobre os utilizadores de bases de dados contidos, consulte [utilizadores de bases de dados contidos - tornando](https://msdn.microsoft.com/library/ff929188.aspx)a sua base de dados portátil .
* Para aprender sobre geo-replicação ativa, consulte [a geo-replicação ativa.](sql-database-active-geo-replication.md)
* Para conhecer os grupos de auto-failover, consulte [os grupos de falha automática](sql-database-auto-failover-group.md).
* Para obter informações sobre o uso de geo-restauro, consulte [geo-restauro](sql-database-recovery-using-backups.md#geo-restore)
