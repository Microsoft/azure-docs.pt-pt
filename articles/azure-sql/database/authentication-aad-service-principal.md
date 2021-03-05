---
title: Azure Ative Directory service principal com Azure SQL
description: As aplicações AD AZure (principais serviços) suportam a criação de utilizadores Azure AD na Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 15cc935457f76fb1d2fe4e8d699db831ebacc357
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181759"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Ative Directory service principal com Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Suporte para a criação de utilizadores do Azure Ative Directory (Azure AD) na Base de Dados Azure SQL (SQL DB) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) em nome das aplicações AD da Azure (principais serviços) estão atualmente em **pré-visualização pública.**

> [!NOTE]
> Esta funcionalidade já está suportada para a SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Suporte principal de serviço (aplicações AD Azure)

Este artigo aplica-se a candidaturas integradas com Azure AD, e fazem parte do registo Azure AD. Estas aplicações muitas vezes precisam de autenticação e acesso de autorização ao Azure SQL para executar várias tarefas. Esta funcionalidade na **pré-visualização pública** permite agora aos diretores de serviço criar utilizadores AD Azure em SQL Database e Azure Synapse. Havia uma limitação que impedia a criação de objetos Azure AD em nome das aplicações AD Azure que foram removidas.

Quando uma aplicação AD Azure é registada usando o portal Azure ou um comando PowerShell, dois objetos são criados no inquilino AD Azure:

- Um objeto da aplicação
- Um objeto do principal de serviço

Para obter mais informações sobre aplicações AD Azure, consulte [os objetos principais de aplicação e serviço no Azure Ative Directory](../../active-directory/develop/app-objects-and-service-principals.md) e [Crie um diretor de serviço Azure com a Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Base de Dados SQL, Azure Synapse e SQL Managed Instance suportam os seguintes objetos AD Azure:

- Utilizadores de AD AZure (geridos, federados e convidados)
- Grupos AD Azure (geridos e federados)
- Aplicações AD AZure 

O comando T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` em nome de uma aplicação AD Azure é agora suportado para A SQL Database e Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funcionalidade da criação de utilizadores Azure AD usando os principais serviços

O suporte a esta funcionalidade é útil nos processos de automatização de aplicações AD do Azure, onde os objetos AD AZure são criados e mantidos na Base de Dados SQL e Azure Synapse sem interação humana. Os principais de serviço podem ser um administrador AD Azure para o servidor lógico SQL, como parte de um grupo ou de um utilizador individual. A aplicação pode automatizar a criação de objetos Azure AD na Base de Dados SQL e Azure Synapse quando executado como administrador de sistema, e não requer quaisquer privilégios SQL adicionais. Isto permite uma automatização completa de uma criação de utilizadores de base de dados. Esta funcionalidade também é suportada pela identidade gerida pelo sistema e pela identidade gerida atribuída pelo utilizador. Para mais informações, veja [quais são as identidades geridas para os recursos da Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Permitir aos principais serviços criar utilizadores AD Azure

Para permitir a criação de um objeto AD Azure na Base de Dados SQL e na Azure Synapse em nome de uma aplicação AD Azure, são necessárias as seguintes definições:

1. Atribua a identidade do servidor. A identidade do servidor atribuída representa a Identidade do Sistema Gerido (MSI). Atualmente, a identidade do servidor do Azure SQL não suporta a Identidade Gerida pelo Utilizador (UMI).
    - Para um novo servidor lógico Azure SQL, execute o seguinte comando PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Para mais informações, consulte o comando [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver)

    - Para os servidores Azure SQL Logical existentes, execute o seguinte comando:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Para mais informações, consulte o comando [Set-AzSqlServer.](/powershell/module/az.sql/set-azsqlserver)

    - Para verificar se a identidade do servidor é atribuída ao servidor, execute o comando Get-AzSqlServer.

    > [!NOTE]
    > A identidade do servidor também pode ser atribuída usando comandos CLI. Para obter mais informações, consulte [a criação do servidor Az Sql](/cli/azure/sql/server#az-sql-server-create) e [a atualização do servidor Az Sql](/cli/azure/sql/server#az-sql-server-update).

2. Conceda ao Azure AD [**Directory Readers**](../../active-directory/roles/permissions-reference.md#directory-readers) a permissão do servidor criado ou atribuído ao servidor.
    - Para conceder esta permissão, siga a descrição utilizada para a SQL Managed Instance que está disponível no seguinte artigo: [Provision Azure AD admin (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - O utilizador Azure AD que está a conceder esta permissão deve fazer parte da função de **Administrador Global** da AD Ad ou administrador de **funções privilegiadas.**

> [!IMPORTANT]
> Os passos 1 e 2 devem ser executados na ordem acima. Em primeiro lugar, criar ou atribuir a identidade do servidor, seguida da concessão da permissão dos [**Leitores de Diretório.**](../../active-directory/roles/permissions-reference.md#directory-readers) Omitir um destes passos, ou ambos causarão um erro de execução durante uma criação de objeto AD Azure em Azure SQL em nome de uma aplicação AD Azure.
>
> Se estiver a utilizar o principal de serviço para definir ou desaparasitar o administrador Azure AD, o pedido também deve ter a permissão de API [de aplicação.Read.All](/graph/permissions-reference#application-permissions-18) Application API em Azure AD. Para obter mais informações sobre [permissões necessárias para definir um administrador AD Azure](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin), e instruções passo a passo para criar um utilizador AD Azure em nome de uma aplicação AD Azure, consulte [Tutorial: Criar utilizadores de AD Azure utilizando aplicações AD Azure](authentication-aad-service-principal-tutorial.md).
>
> Na **pré-visualização pública,** pode atribuir o papel **de Leitores** de Diretório a um grupo em Azure AD. Os proprietários do grupo podem então adicionar a identidade gerida como membro deste grupo, o que contornaria a necessidade de um **Administrador Global** ou Administrador **de Funções Privilegiadas** conceder o papel de Leitores de **Diretório.** Para obter mais informações sobre esta funcionalidade, consulte o [papel dos Leitores de Diretório no Diretório Ativo Azure para a Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Resolução de problemas e limitações para visualização pública

- Ao criar objetos AZure AD em Azure SQL em nome de uma aplicação AD Azure sem permitir a identidade do servidor e conceder permissão aos Leitores de **Diretório,** a operação falhará com os seguintes erros possíveis. O exemplo abaixo é para uma execução de comando PowerShell para criar um utilizador de base de dados SQL `myapp` no artigo [Tutorial: Criar utilizadores de AD Azure usando aplicações AD Azure](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Para o erro acima, siga os passos para [atribuir uma identidade ao servidor lógico Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) e atribuir [permissão aos Leitores de Diretório para a identidade do servidor lógico SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > As mensagens de erro indicadas acima serão alteradas antes da funcionalidade GA para identificar claramente o requisito de configuração em falta para o suporte à aplicação AD do Azure.
- Definir a aplicação AD AZure como um administrador AD Azure para SQL Managed Instance é suportado apenas usando o comando CLI, e o comando PowerShell com [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou superior. Para obter mais informações, consulte os comandos [az sql mi admin](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) e os comandos [Set-AzSqlInstanceActiveDirectoryAdministrator.](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 
    - Se pretender utilizar o portal Azure para a SQL Managed Instance para definir o administrador AD Azure, uma possível solução é criar um grupo AD Azure. Em seguida, adicione o principal de serviço (aplicação AD AD) a este grupo, e coloque este grupo como um administrador AD Azure para a SQL Managed Instance.
    - A definição do principal de serviço (aplicação Azure AD) como um administrador AD AZure para base de dados SQL e Azure Synapse é suportado através dos comandos Azure portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)e [CLI.](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)
- A utilização de uma aplicação AD AZure com o principal serviço de outro inquilino AZure AD falhará ao aceder à Base de Dados SQL ou à SQL Managed Instance criada num inquilino diferente. Um principiante de serviço atribuído a esta aplicação deve ser do mesmo inquilino que o servidor lógico SQL ou a Instância Gerida.
- O módulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) ou superior é necessário ao utilizar o PowerShell para configurar uma aplicação AD AD Azure individual como administrador Azure AD para Azure SQL. Certifique-se de que está atualizado para o módulo mais recente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar utilizadores de AD Azure usando aplicações AD Azure](authentication-aad-service-principal-tutorial.md)