---
title: Papel de Leitores de Diretório no Azure Ative Directory para Azure SQL
description: Conheça o papel do leitor de diretório no Azure AD para a Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5764a8df862610fc076ce2810fcc0d4bf8dbda3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094561"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Papel de Leitores de Diretório no Azure Ative Directory para Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Esta característica neste artigo está em **visualização pública.**

O Azure Ative Directory (Azure AD) introduziu [a utilização de grupos de nuvem para gerir atribuições de funções no Azure Ative Directory (pré-visualização)](../../active-directory/roles/groups-concept.md). Isto permite que as funções AZURE AD sejam atribuídas a grupos.

Ao permitir uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para a Base de Dados Azure SQL, Instância Gerida Azure SQL ou Azure Synapse Analytics, o papel [**de Leitores de Diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) AD Azure deve ser atribuído à identidade para permitir o acesso lido à [API do Gráfico AD Azure](../../active-directory/develop/active-directory-graph-api.md). A identidade gerida da SQL Database e da Azure Synapse é referida como a identidade do servidor. A identidade gerida de SQL Managed Instance é referida como a identidade de instância gerida, e é automaticamente atribuída quando o caso é criado. Para obter mais informações sobre a atribuição de uma identidade de servidor à Base de Dados SQL ou Azure Synapse, consulte [Ativar os principais serviços para criar utilizadores AD Azure](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

O papel **de Leitores de Diretório** é necessário para:

- Criar logins AD Azure para sql gestão de instâncias
- Personificar utilizadores da AZure AD no Azure SQL
- Migrar os utilizadores do SQL Server que utilizam a autenticação do Windows para a SQL Managed Instance com autenticação AD Azure (utilizando o comando [ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration)
- Alterar o administrador AD Ad Azure para a SQL Managing Instance
- Permitir que [os principais de serviço (Aplicações)](authentication-aad-service-principal.md) criem utilizadores AD Azure em Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Atribuindo o papel de Leitores de Diretório

Para atribuir o papel de Leitores de [**Diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) a uma identidade, é necessário um utilizador com permissões [de Administrador Global](../../active-directory/roles/permissions-reference.md#global-administrator) ou Administrador [privilegiado.](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) Os utilizadores que frequentemente gerem ou implementam a BASE de Dados SQL, SQL Managed Instance ou Azure Synapse podem não ter acesso a estas funções altamente privilegiadas. Isto pode muitas vezes causar complicações para os utilizadores que criam recursos Azure SQL não planeados, ou precisam da ajuda de membros altamente privilegiados que são muitas vezes inacessíveis em grandes organizações.

Para a SQL Managed Instance, o papel **de Leitores** de Diretório deve ser atribuído à identidade de exemplo gerida antes de poder [configurar um administrador AD Azure para o caso gerido](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

A atribuição do papel **de Leitores** de Diretório à identidade do servidor não é necessária para a Base de Dados SQL ou Azure Synapse ao configurar um administrador AD Azure para o servidor lógico. No entanto, para permitir a criação de um objeto AZure AD na Base de Dados SQL ou Azure Synapse em nome de uma aplicação AD Azure, é necessário o papel de Leitores de **Diretório.** Se a função não for atribuída à identidade lógica do servidor SQL, a criação de utilizadores AD Azure em Azure SQL falhará. Para mais informações, consulte [o diretor de serviço do Azure Ative Directory com a Azure SQL.](authentication-aad-service-principal.md)

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Concessão do papel de Leitores de Diretório a um grupo AD Azure

Atualmente em **pré-visualização pública,** pode agora ter um [Administrador Global](../../active-directory/roles/permissions-reference.md#global-administrator) ou [Administrador de Função Privilegiada](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) criar um grupo AD Azure e atribuir a permissão dos Leitores de [**Diretório**](../../active-directory/roles/permissions-reference.md#directory-readers) ao grupo. Isto permitirá o acesso à Azure AD Graph API para os membros deste grupo. Além disso, os utilizadores AZURE AD que são proprietários deste grupo estão autorizados a atribuir novos membros para este grupo, incluindo identidades dos servidores lógicos Azure SQL.

Esta solução ainda requer um utilizador de alto privilégio (Administrador Global ou Administrador de Função Privilegiado) para criar um grupo e atribuir os utilizadores como uma atividade única, mas os proprietários do grupo AZURE AD poderão atribuir membros adicionais para a frente. Isto elimina a necessidade de envolver um utilizador de alto privilégio no futuro para configurar todas as bases de dados SQL, SQL Managed Instances ou Azure Synapse servidores no seu inquilino AZure AD.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Atribuir papel de Leitores de Diretório a um grupo AD Azure e gerir atribuições de funções](authentication-aad-directory-readers-role-tutorial.md)