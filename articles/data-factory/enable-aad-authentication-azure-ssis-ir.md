---
title: Ativar a autenticação do Azure Active Directory para o Runtime de integração de SSIS do Azure | Documentos da Microsoft
description: Este artigo descreve como ativar a autenticação do Azure Active Directory com a identidade gerida do Azure Data Factory criar o Runtime de integração Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 3/11/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 787c436261635376ff82e8762cbc1469f4375e6b
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729957"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Ativar a autenticação do Azure Active Directory para o Runtime de integração Azure-SSIS

Este artigo mostra-lhe como ativar a autenticação do Azure Active Directory (Azure AD) com a identidade gerida para o Azure Data Factory (ADF) e utilizá-lo em vez da autenticação de SQL para criar um IR Azure-SSIS Integration Runtime () que por sua vez irá aprovisionar SSIS catálogo base de dados (SSISDB) na base de dados do Azure SQL server/gerida instância em seu nome.

Para mais informações sobre a identidade gerida para o ADF, veja [identiy gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Neste cenário, autenticação do Azure AD com a identidade gerida para o ADF só é utilizada na criação e as operações subsequentes iniciais do Runtime de integração do SSIS que será em Ativar aprovisionar e ligar ao SSISDB. Para execuções de pacote do SSIS, o runtime de integração de SSIS ainda se conectará ao SSISDB utilizando a autenticação SQL com contas totalmente geridas que são criadas durante o aprovisionamento de SSISDB.
>-  Se já tiver criado o runtime de integração de SSIS através da autenticação de SQL, não pode reconfigurá-la para utilizar a autenticação do Azure AD através do PowerShell neste momento, mas pode fazê-lo através da aplicação do Azure portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Ativar o Azure AD na base de dados SQL do Azure

O servidor de base de dados SQL do Azure suporta a criação de uma base de dados com um utilizador do Azure AD. Em primeiro lugar, terá de criar um grupo do Azure AD com a identidade gerida para o ADF como membro. Em seguida, terá de definir um utilizador do Azure AD como o administrador do Active Directory para o seu servidor de base de dados do Azure SQL e, em seguida, ligá-la no SQL Server Management Studio (SSMS) utilizando esse usuário. Por fim, tem de criar um usuário independente, que representa o grupo do Azure AD, para que a identidade gerida para o ADF pode ser utilizada pelo Runtime de integração Azure-SSIS para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Criar um grupo do Azure AD com a identidade gerida para o ADF como membro

Pode utilizar um grupo do Azure AD existente ou crie um novo com o Azure AD PowerShell.

1.  Instalar o [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) módulo.

2.  Inicie sessão com `Connect-AzureAD`, execute o seguinte cmdlet para criar um grupo e guarde-o numa variável:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    O resultado é semelhante ao exemplo seguinte, o que também apresenta o valor da variável:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adicione a identidade gerida para o ADF ao grupo. Pode seguir o artigo [identiy gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter o ID de identidade de serviço principal (por exemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mas não utilize ID de aplicação de identidade de serviço para esta finalidade).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Também pode verificar a associação ao grupo posteriormente.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurar a autenticação do Azure AD para o servidor de base de dados do Azure SQL

Pode [configurar e gerir a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) através dos seguintes passos:

1.  No portal do Azure, selecione **todos os serviços** -> **servidores SQL** no painel de navegação esquerda.

2.  Selecione o seu servidor de base de dados do Azure SQL seja configurado com a autenticação do Azure AD.

3.  Na **configurações** seção do painel, selecione **administrador do Active Directory**.

4.  Na barra de comandos, selecione **definir administrador**.

5.  Selecione uma conta de utilizador do Azure AD que se tornarão o administrador do servidor e, em seguida, selecione **selecionar.**

6.  Na barra de comandos, selecione **guardar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um usuário independente no servidor de base de dados do Azure SQL, que representa o grupo do Azure AD

Esta próxima etapa, precisa [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Na **ligar ao servidor** caixa de diálogo, introduza o nome do servidor de base de dados do Azure SQL no **nome do servidor** campo.

3.  Na **autenticação** campo, selecione **Active Directory - Universal com o suporte MFA** (também pode utilizar os outros dois tipos de autenticação do Active Directory, consulte [ Configurar e gerir a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4.  Na **nome de utilizador** , insira o nome da conta do Azure AD que definiu como administrador do servidor, por exemplo, testuser@xxxonline.com.

5.  Selecione **Connect** e concluir o processo de início de sessão.

6.  Na **Object Explorer**, expanda o **bases de dados** -> **bases de dados do sistema** pasta.

7.  Com o botão direito no **mestre** da base de dados e selecione **nova consulta**.

8.  Na janela da consulta, introduza o seguinte comando do T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, a criação de um usuário independente para representar o grupo.

9.  Limpar a janela de consulta, introduza o seguinte comando do T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com êxito, o usuário independente a conceder a capacidade de criar uma base de dados (SSISDB).

10.  Se o SSISDB foi criado utilizando a autenticação SQL e pretender passar a utilizar a autenticação do Azure AD para o runtime de integração Azure-SSIS para aceder ao mesmo, faça duplo clique em **SSISDB** da base de dados e selecione **nova consulta**.

11.  Na janela da consulta, introduza o seguinte comando do T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, a criação de um usuário independente para representar o grupo.

12.  Limpar a janela de consulta, introduza o seguinte comando do T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com êxito, o usuário independente a conceder a capacidade de acessar o SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Ativar o Azure AD na instância gerida de base de dados SQL do Azure

Instância de gerida de base de dados de SQL do Azure suporta a criação de uma base de dados com a identidade gerida para o ADF diretamente. Não precisa de associar-se a identidade gerida para o ADF para um grupo do Azure AD nem criar um usuário independente, que representa esse grupo em sua instância gerida.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurar a autenticação do Azure AD para a instância gerida da base de dados SQL do Azure

1.   No portal do Azure, selecione **todos os serviços** -> **servidores SQL** no painel de navegação esquerda.

2.   Selecione a sua instância gerida, ser configurado com a autenticação do Azure AD.

3.   Na **configurações** seção do painel, selecione **administrador do Active Directory**.

4.   Na barra de comandos, selecione **definir administrador**.

5.   Selecione uma conta de utilizador do Azure AD que se tornarão o administrador do servidor e, em seguida, selecione **selecione**.

6.   Na barra de comandos, selecione **guardar**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicionar a identidade gerida para o ADF como um utilizador na instância gerida da base de dados SQL do Azure

Esta próxima etapa, precisa [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Ligar à sua instância gerida com a sua conta de administrador do SQL/Active Directory.

3.  Na **Object Explorer**, expanda o **bases de dados** -> **bases de dados do sistema** pasta.

4.  Com o botão direito no **mestre** da base de dados e selecione **nova consulta**.

5.  Obter a identidade gerida para o ADF. Pode seguir o artigo [identiy gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter o ID de aplicação de identidade de serviço principal (mas não utilizar o ID de identidade de serviço para esta finalidade).

6.  Na janela de consulta, execute o seguinte script de T-SQL para converter a identidade gerida para o ADF para tipo binário:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your SERVICE IDENTITY APPLICATION ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    O comando deve ser concluído com êxito, exibindo a identidade gerida para o ADF como binário.

7.  Limpar a janela de consulta e execute o seguinte script de T-SQL para adicionar a identidade gerida para o ADF como um utilizador

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    O comando deve ser concluído com êxito, concedendo a identidade gerida para o ADF a capacidade de criar uma base de dados (SSISDB).

8.  Se o SSISDB foi criado utilizando a autenticação SQL e pretender passar a utilizar a autenticação do Azure AD para o runtime de integração Azure-SSIS para aceder ao mesmo, faça duplo clique em **SSISDB** da base de dados e selecione **nova consulta**.

9.  Na janela da consulta, introduza o seguinte comando do T-SQL e selecione **Execute** na barra de ferramentas.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    O comando deve ser concluído com êxito, concedendo a identidade gerida para o ADF a capacidade de acessar o SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Aprovisionar o IR de Azure-SSIS na aplicação de portal/ADF do Azure

Quando aprovisionar o runtime de integração Azure-SSIS na aplicação de portal/ADF do Azure, no **definições do SQL** página, selecione **autenticação do AAD de utilização com a identidade gerida para o ADF** opção. Captura de ecrã seguinte mostra as definições para o runtime de integração com o servidor de base de dados do Azure SQL a alojar o SSISDB. Para o runtime de integração com a instância gerida a alojar o SSISDB, o **camada de serviço de base de dados do catálogo** e **dos serviços de permitir que o Azure para o acesso** definições não são aplicáveis, embora outras configurações sejam os mesmos.

Para mais informações sobre como criar um IR Azure-SSIS, veja [criar um runtime de integração Azure-SSIS no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Definições para o runtime de integração Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Aprovisionar o IR de Azure-SSIS com o PowerShell

Para aprovisionar o runtime de integração Azure-SSIS com o PowerShell, efetue os seguintes procedimentos:

1.  Instale [do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) módulo.

2.  No seu script, não defina `CatalogAdminCredential` parâmetro. Por exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
