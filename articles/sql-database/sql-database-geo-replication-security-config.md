---
title: Configurar a segurança da base de dados do Azure SQL para recuperação após desastre | Documentos da Microsoft
description: Conheça as considerações de segurança para configurar e gerir a segurança após um restauro de base de dados ou uma ativação pós-falha para um servidor secundário.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 50cec0774729073678498922b2c58f1ca62d7d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341952"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Configurar e gerir a segurança de base de dados do Azure SQL para o restauro geográfico ou de ativação pós-falha

Este artigo descreve os requisitos de autenticação para configurar e controlar [georreplicação ativa](sql-database-active-geo-replication.md) e [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md). Ele também fornece os passos necessários para configurar o acesso de utilizador para a base de dados secundário. Por fim, ele também descreve como ativar o acesso à base de dados recuperada depois de usar [georrestauro](sql-database-recovery-using-backups.md#geo-restore). Para obter mais informações sobre as opções de recuperação, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Recuperação após desastre com utilizadores contidos

Ao contrário dos utilizadores tradicionais, o que devem ser mapeados para inícios de sessão na base de dados mestra, um usuário independente é totalmente gerido pela base de dados em si. Isso tem dois benefícios. No cenário de recuperação após desastre, os utilizadores podem continuar a ligar à base de dados primária nova ou a base de dados recuperada com restauro geográfico sem qualquer configuração adicional, porque a base de dados gere os utilizadores. Também existem potencial de escalabilidade e os benefícios de desempenho com esta configuração de uma perspectiva de início de sessão. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).

A desvantagem principal é gerenciar o processo de recuperação após desastre à escala mais desafiador. Quando tem várias bases de dados que utilizam o mesmo início de sessão, manter as credenciais com utilizadores contidos em várias bases de dados pode negar os benefícios de utilizadores contidos. Por exemplo, a política de rotação da palavra-passe requer que as alterações ser feitas de forma consistente em várias bases de dados em vez de alterar a palavra-passe para início de sessão uma vez na base de dados mestra. Por esse motivo, se tiver várias bases de dados que utilizam o mesmo nome de utilizador e palavra-passe, através de utilizadores contidos não é recomendado.

## <a name="how-to-configure-logins-and-users"></a>Como configurar utilizadores e inícios de sessão

Se estiver a utilizar inícios de sessão e utilizadores (em vez de utilizadores contidos), tem de efetuar passos adicionais para garantir que os inícios de sessão mesmo existem na base de dados mestra. As secções seguintes descrevem as considerações de etapas envolvidas e adicionais.

  >[!NOTE]
  > Também é possível utilizar inícios de sessão do Azure Active Directory (AAD) para gerir as bases de dados. Para obter mais informações, consulte [inícios de sessão do SQL do Azure e os utilizadores](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Configurar o acesso de utilizador para uma base de dados secundária ou recuperada

Por ordem para a base de dados secundária para ser utilizado como uma base de dados secundária só de leitura e para garantir um acesso adequado para a nova base de dados primária ou a base de dados recuperada com restauro geográfico, base de dados mestra do servidor de destino tem de ter a segurança adequada configuração no local antes da recuperação.

As permissões específicas para cada passo são descritas posteriormente neste tópico.

Preparar o acesso de utilizador para uma secundária de georreplicação deve ser efetuada como parte de configurar a georreplicação. Preparar o acesso de utilizador para as bases de dados geo-restaurada deve ser efetuado em qualquer altura, quando o servidor original está online (por exemplo, como parte de exploração de DR).

> [!NOTE]
> Se efetuar a ativação pós-falha ou o restauro geográfico para um servidor que não tem inícios de sessão corretamente configurados, acesso ao mesmo será limitado para a conta de administrador do servidor.

Configuração de inícios de sessão no servidor de destino envolve três passos descritos abaixo:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Determinar os inícios de sessão com acesso à base de dados primária

A primeira etapa do processo é determinar quais logons devem ser duplicados no servidor de destino. Isto é conseguido com um par de instruções SELECT, da base de dados mestra lógica no servidor de origem e da base de dados primária em si.

Apenas o administrador do servidor ou membro do **LoginManager** função de servidor pode determinar os inícios de sessão no servidor de origem com a seguinte instrução SELECT.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Apenas um membro da função de base de dados db_owner, o utilizador dbo ou administrador de servidor, pode determinar todos os principais de utilizador de base de dados na base de dados primária.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Localizar o SID de inícios de sessão identificados no passo 1

Ao comparar o resultado das consultas da secção anterior e os SIDs de correspondência, pode mapear o início de sessão do servidor para o utilizador de base de dados. Inícios de sessão que têm um utilizador de base de dados com um SID correspondente tem acesso de utilizador para essa base de dados como esse principal de utilizador de base de dados.

A seguinte consulta pode ser utilizada para ver todas as entidades de usuário e seu SID numa base de dados. Apenas um membro do administrador de função ou o servidor de base de dados db_owner pode executar esta consulta.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> O **INFORMATION_SCHEMA** e **sys** os utilizadores têm *nulo* SIDs e a **convidado** SID é **0x00**. O **dbo** SID pode começar pelo *0x01060000000001648000000000048454*, se o criador de base de dados foi o administrador do servidor em vez de um membro da **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Criar os inícios de sessão no servidor de destino

A última etapa é ir para o servidor de destino ou para servidores e gerar os inícios de sessão com as SIDs apropriado. Segue-se a sintaxe básica.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Se quiser conceder acesso de utilizador para o secundário, mas não para o primário, pode fazê-lo ao alterar o início de sessão do utilizador no servidor primário, utilizando a seguinte sintaxe.
>
> ALTERAR INÍCIO DE SESSÃO <login name> DESATIVAR
>
> DESATIVAR não altera a palavra-passe, para que pode sempre ativá-la se for necessário.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a gestão de acesso de base de dados e inícios de sessão, consulte [segurança da base de dados SQL: Gerir a segurança de acesso e o início de sessão da base de dados](sql-database-manage-logins.md).
* Para obter mais informações sobre os utilizadores de base de dados contida, consulte [contidos utilizadores de base - tornar a base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).
* Para saber mais sobre a georreplicação ativa, veja [georreplicação ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de ativação pós-falha automática, consulte [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
* Para obter informações sobre como utilizar o restauro geográfico, consulte [georrestauro](sql-database-recovery-using-backups.md#geo-restore)
