---
title: Habilitar a autenticação de Azure Active Directory para o Azure-SSIS Integration Runtime | Microsoft Docs
description: Este artigo descreve como habilitar a autenticação de Azure Active Directory com a identidade gerenciada para Azure Data Factory para criar Integration Runtime do Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 1e55d1878b1a5616d467f2fa27b1b20132d5e77c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516993"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Habilitar a autenticação de Azure Active Directory para o Azure-SSIS Integration Runtime

Este artigo mostra como habilitar a autenticação do Azure Active Directory (AD do Azure) com a identidade gerenciada para seu Azure Data Factory (ADF) e usá-la em vez da autenticação do SQL para criar um IR (Azure-SSIS Integration Runtime) que, por sua vez, provisionará Banco de dados de catálogo do SSIS (SSISDB) no servidor de banco de dados SQL do Azure/Instância Gerenciada em seu nome.

Para obter mais informações sobre a identidade gerenciada para o ADF, consulte [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Nesse cenário, a autenticação do Azure AD com a identidade gerenciada para o ADF só é usada nas operações de criação e início subsequentes do seu IR do SSIS que, por sua vez, provisionará e se conectará ao SSISDB. Para execuções de pacote SSIS, seu IR do SSIS ainda se conectará ao SSISDB usando a autenticação do SQL com contas totalmente gerenciadas que são criadas durante o provisionamento do SSISDB.
>-  Se você já tiver criado seu IR do SSIS usando a autenticação do SQL, não poderá reconfigurá-lo para usar a autenticação do Azure AD por meio do PowerShell no momento, mas você pode fazer isso por meio do aplicativo portal do Azure/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Habilitar o Azure AD no banco de dados SQL do Azure

O servidor de banco de dados SQL do Azure suporta a criação de um banco de dados com um usuário do Azure AD Primeiro, você precisa criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro. Em seguida, você precisa definir um usuário do Azure AD como o administrador do Active Directory para o servidor do banco de dados SQL do Azure e, em seguida, conectar-se a ele no SQL Server Management Studio (SSMS) usando esse usuário. Por fim, você precisa criar um usuário independente que representa o grupo do Azure AD, portanto, a identidade gerenciada para o ADF pode ser usada pelo IR do Azure-SSIS para criar SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Criar um grupo do Azure AD com a identidade gerenciada para o ADF como um membro

Você pode usar um grupo existente do Azure AD ou criar um novo usando o PowerShell do Azure AD.

1.  Instalar o [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) módulo.

2.  Entre usando `Connect-AzureAD`, execute o seguinte cmdlet para criar um grupo e salve-o em uma variável:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    O resultado é semelhante ao exemplo a seguir, que também exibe o valor da variável:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adicione a identidade gerenciada para o ADF ao grupo. Você pode seguir o artigo [Managed identiy para data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) obter a ID de objeto de identidade gerenciada principal (por exemplo, 765AD4AB-XXXX-XXXX-XXXX-51ed985819dc, mas não use a ID de aplicativo de identidade gerenciada para essa finalidade).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Você também pode verificar a associação de grupo posteriormente.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configurar a autenticação do Azure AD para o servidor de banco de dados SQL do Azure

Você pode [configurar e gerenciar a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) usando as seguintes etapas:

1.  Em portal do Azure, selecione **todos os serviços** -> **SQL Servers** na navegação à esquerda.

2.  Selecione o servidor do banco de dados SQL do Azure a ser configurado com a autenticação do Azure AD.

3.  Na seção **configurações** da folha, selecione **Active Directory admin**.

4.  Na barra de comandos, selecione **definir administrador**.

5.  Selecione uma conta de usuário do Azure AD para tornar o administrador do servidor e selecione **selecionar.**

6.  Na barra de comandos, selecione **salvar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um usuário independente no servidor do banco de dados SQL do Azure que representa o grupo do Azure AD

Para esta próxima etapa, você precisará de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Inicie o SSMS.

2. Na caixa de diálogo **conectar ao servidor** , insira o nome do servidor do banco de dados SQL do Azure no campo **nome do servidor** .

3. No campo **autenticação** , selecione **Active Directory-universal com suporte a MFA** (você também pode usar os outros dois tipos de autenticação Active Directory, consulte [configurar e gerenciar a autenticação do Azure AD com o SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. No campo **nome de usuário** , digite o nome da conta do Azure AD que você definiu como o administrador do servidor testuser@xxxonline.com, por exemplo,.

5. Selecione **conectar** e conclua o processo de entrada.

6. No Pesquisador de **objetos**, expanda **a pasta** -> bancos de dados**sistema** bancos de dados.

7. Clique com o botão direito do mouse em banco de dados **mestre** e selecione **nova consulta**.

8. Na janela de consulta, insira o comando T-SQL a seguir e selecione **executar** na barra de ferramentas.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

9. Desmarque a janela de consulta, insira o comando T-SQL a seguir e selecione **executar** na barra de ferramentas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de criar um banco de dados (SSISDB).

10. Se o SSISDB foi criado usando a autenticação do SQL e você deseja alternar para usar a autenticação do Azure AD para o IR do Azure-SSIS para acessá-lo, clique com o botão direito do mouse no banco de dados **SSISDB** e selecione **nova consulta**.

11. Na janela de consulta, insira o comando T-SQL a seguir e selecione **executar** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, criando um usuário independente para representar o grupo.

12. Desmarque a janela de consulta, insira o comando T-SQL a seguir e selecione **executar** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com êxito, concedendo ao usuário independente a capacidade de acessar o SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Habilitar o Azure AD no Instância Gerenciada do Banco de Dados SQL do Azure

Instância Gerenciada do Banco de Dados SQL do Azure dá suporte à criação de um banco de dados com a identidade gerenciada diretamente para o ADF. Você não precisa ingressar a identidade gerenciada do ADF em um grupo do Azure AD nem criar um usuário independente que represente esse grupo em seu Instância Gerenciada.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configurar a autenticação do Azure AD para o Instância Gerenciada do Banco de Dados SQL do Azure

Siga as etapas em [provisionar um administrador de Azure Active Directory para seu instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicione a identidade gerenciada para o ADF como um usuário no Instância Gerenciada do Banco de Dados SQL do Azure

Para esta próxima etapa, você precisará de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie o SSMS.

2.  Conecte-se ao seu Instância Gerenciada usando uma conta de SQL Server que seja um **sysadmin**. Essa é uma limitação temporária que será removida quando as entidades de segurança do Azure AD Server (logons) para Instância Gerenciada do Banco de Dados SQL do Azure se tornarem GA. Você verá o erro a seguir se tentar usar uma conta de administrador do Azure AD para criar o logon: MSG 15247, nível 16, estado 1, o usuário da linha 1 não tem permissão para executar esta ação.

3.  No Pesquisador de **objetos**, expanda **a pasta** -> bancos de dados**sistema** bancos de dados.

4.  Clique com o botão direito do mouse em banco de dados **mestre** e selecione **nova consulta**.

5.  Na janela de consulta, execute o seguinte script T-SQL para adicionar a identidade gerenciada para o ADF como um usuário

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de criar um banco de dados (SSISDB).

6.  Se o SSISDB foi criado usando a autenticação do SQL e você deseja alternar para usar a autenticação do Azure AD para o IR do Azure-SSIS para acessá-lo, clique com o botão direito do mouse no banco de dados **SSISDB** e selecione **nova consulta**.

7.  Na janela de consulta, insira o comando T-SQL a seguir e selecione **executar** na barra de ferramentas.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    O comando deve ser concluído com êxito, concedendo à identidade gerenciada para o ADF a capacidade de acessar o SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provisionar IR do Azure-SSIS no aplicativo portal do Azure/ADF

Ao provisionar o IR do Azure-SSIS no aplicativo portal do Azure/ADF, na página **configurações do SQL** , selecione **usar a autenticação do AAD com a identidade gerenciada para a opção ADF** . A captura de tela a seguir mostra as configurações de IR com o servidor de banco de dados SQL do Azure que hospeda o SSISDB. Para IR com Instância Gerenciada que hospeda o SSISDB, a **camada de serviço do banco de dados do catálogo** e **permite que os serviços do Azure acessem** as configurações não são aplicáveis, enquanto outras configurações são as mesmas.

Para obter mais informações sobre como criar um IR do Azure-SSIS, consulte [criar um tempo de execução de integração do Azure-SSIS no Azure data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Configurações para o tempo de execução de integração do Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provisionar o IR do Azure-SSIS com o PowerShell

Para provisionar o IR do Azure-SSIS com o PowerShell, faça o seguinte:

1.  Instale o módulo [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Em seu script, não defina `CatalogAdminCredential` o parâmetro. Por exemplo:

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
