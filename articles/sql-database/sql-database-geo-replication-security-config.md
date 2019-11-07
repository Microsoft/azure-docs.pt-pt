---
title: Configurar a segurança do banco de dados SQL para recuperação de desastre
description: Conheça as considerações de segurança para configurar e gerenciar a segurança após uma restauração de banco de dados ou um failover para um servidor secundário.
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
ms.openlocfilehash: 3c08ba1a37d7b0d16042d6496c27e0de8d070b75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689980"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configurar e gerenciar a segurança do banco de dados SQL do Azure para restauração geográfica ou failover

Este artigo descreve os requisitos de autenticação para configurar e controlar a [replicação geográfica ativa](sql-database-active-geo-replication.md) e os [grupos de failover automático](sql-database-auto-failover-group.md). Ele também fornece as etapas necessárias para configurar o acesso do usuário ao banco de dados secundário. Por fim, ele também descreve como habilitar o acesso ao banco de dados recuperado depois [de usar a restauração geográfica](sql-database-recovery-using-backups.md#geo-restore). Para obter mais informações sobre as opções de recuperação, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Recuperação de desastres com usuários independentes

Ao contrário dos usuários tradicionais, que devem ser mapeados para logons no banco de dados mestre, um usuário independente é totalmente gerenciado pelo próprio banco de dados. Isso tem dois benefícios. No cenário de recuperação de desastres, os usuários podem continuar a se conectar ao novo banco de dados primário ou ao banco de dados recuperado usando a restauração geográfica sem nenhuma configuração adicional, pois o banco de dados gerencia os usuários. Também há possíveis benefícios de escalabilidade e desempenho dessa configuração a partir de uma perspectiva de logon. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).

A principal desvantagem é que o gerenciamento do processo de recuperação de desastre em escala é mais desafiador. Quando você tem vários bancos de dados que usam o mesmo logon, manter as credenciais usando usuários independentes em vários bancos de dados pode negar os benefícios dos usuários independentes. Por exemplo, a política de rotação de senha requer que as alterações sejam feitas consistentemente em vários bancos de dados em vez de alterar a senha para o logon uma vez no banco de dados mestre. Por esse motivo, se você tiver vários bancos de dados que usam o mesmo nome de usuário e senha, o uso de usuários independentes não será recomendado.

## <a name="how-to-configure-logins-and-users"></a>Como configurar logons e usuários

Se você estiver usando logons e usuários (em vez de usuários independentes), deverá executar etapas adicionais para garantir que os mesmos logons existam no banco de dados mestre. As seções a seguir descrevem as etapas envolvidas e considerações adicionais.

  >[!NOTE]
  > Também é possível usar os logons Azure Active Directory (AAD) para gerenciar seus bancos de dados. Para obter mais informações, consulte [logons e usuários do SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso do usuário a um banco de dados secundário ou recuperado

Para que o banco de dados secundário seja utilizável como um banco de dados secundário somente leitura e para garantir o acesso apropriado ao novo banco de dados primário ou ao banco de dados recuperado usando a restauração geográfica, o banco de dados mestre do servidor de destino deve ter a segurança apropriada configuração no local antes da recuperação.

As permissões específicas para cada etapa são descritas posteriormente neste tópico.

A preparação do acesso do usuário a uma replicação geográfica secundária deve ser executada como parte Configurando a replicação geográfica. A preparação do acesso do usuário aos bancos de dados restaurados geograficamente deve ser executada a qualquer momento quando o servidor original estiver online (por exemplo, como parte da análise de DR).

> [!NOTE]
> Se você realizar o failover ou a restauração geográfica em um servidor que não tenha logons configurados corretamente, o acesso a ele será limitado à conta do administrador do servidor.

A configuração de logons no servidor de destino envolve três etapas descritas abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. determinar logons com acesso ao banco de dados primário

A primeira etapa do processo é determinar quais logons devem ser duplicados no servidor de destino. Isso é feito com um par de instruções SELECT, uma no banco de dados mestre lógico no servidor de origem e outra no próprio banco de dados primário.

Somente o administrador do servidor ou um membro da função de servidor **loginmanager** pode determinar os logons no servidor de origem com a instrução SELECT a seguir.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Somente um membro da função de banco de dados db_owner, o usuário dbo ou o administrador do servidor, pode determinar todas as entidades de usuário do banco de dados no banco de dados primário.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Localize o SID para os logons identificados na etapa 1

Comparando a saída das consultas da seção anterior e correspondendo aos SIDs, você pode mapear o logon do servidor para o usuário do banco de dados. Os logons que têm um usuário de banco de dados com um SID correspondente têm acesso de usuário a esse banco de dados como a entidade de usuário do banco de dados.

A consulta a seguir pode ser usada para ver todas as entidades de usuário e seus SIDs em um banco de dados. Somente um membro da função de banco de dados db_owner ou administrador do servidor pode executar esta consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Os usuários **INFORMATION_SCHEMA** e **Sys** têm SIDs *nulos* e o Sid de **convidado** é **0x00**. O Sid de **dbo** pode começar com *0x01060000000001648000000000048454*, se o criador do banco de dados era o administrador do servidor em vez de um membro do **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. criar os logons no servidor de destino

A última etapa é ir para o servidor de destino, ou servidores, e gerar os logons com os SIDs apropriados. A sintaxe básica é a seguinte:

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Se você quiser conceder acesso ao usuário para o secundário, mas não para o primário, poderá fazer isso alterando o logon do usuário no servidor primário usando a sintaxe a seguir.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Desabilitar não altera a senha, para que você sempre possa habilitá-la se necessário.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como gerenciar o acesso ao banco de dados e os logons, consulte segurança do banco de dados [SQL: gerenciar acesso ao banco de dados e segurança](sql-database-manage-logins.md)
* Para obter mais informações sobre usuários de banco de dados independente, consulte [usuários de banco de dados independente-tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).
* Para saber mais sobre a replicação geográfica ativa, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de failover automático, consulte [grupos de failover automático](sql-database-auto-failover-group.md).
* Para obter informações sobre como usar a restauração geográfica, consulte [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore)
