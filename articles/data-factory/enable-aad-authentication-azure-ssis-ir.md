---
title: Ativar a AAD para o tempo de execução de integração Azure SSIS
description: Este artigo descreve como permitir a autenticação do Diretório Ativo Azure com a identidade gerida para a Azure Data Factory criar o Tempo de Funcionatão de Integração Azure-SSIS.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 77b49d8446c7882a155742e8455d77bd1ec110cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606195"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Ativar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo mostra-lhe como permitir a autenticação do Azure Ative Directory (Azure AD) com a identidade gerida para a sua Fábrica de Dados Azure (ADF) e usá-lo em vez de métodos convencionais de autenticação (como a autenticação SQL) para:

- Crie um Tempo de Execução de Integração Azure-SSIS (IR) que, por sua vez, fornecerá base de dados de catálogo SSISIS (SSISDB) no servidor de base de dados Azure SQL/Instância Gerida em seu nome.

- Ligue-se a vários recursos Azure ao executar pacotes SSIS no Ir Azure-SSIS.

Para obter mais informações sobre a identidade gerida para a sua ADF, consulte [a identidade gerida para data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  Neste cenário, a autenticação Da Azure Com a identidade gerida para o seu ADF só é utilizada na criação e subsequentes operações de arranque do seu IR SSIS que, por sua vez, fornecerão e ligarão ao SSISDB. Para execuções de pacotes SSIS, o seu SSIS IR continuará a ligar-se ao SSISDB utilizando a autenticação SQL com contas totalmente geridas que são criadas durante o provisionamento SSISDB.
>-  Se já criou o seu SSIS IR utilizando a autenticação SQL, não pode reconfigurá-lo para utilizar a autenticação Azure AD via PowerShell neste momento, mas pode fazê-lo através da aplicação Portal Azure/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Ativar a AD Azure na base de dados Azure SQL

O servidor de base de dados Azure SQL suporta a criação de uma base de dados com um utilizador de Anúncios Azure. Em primeiro lugar, é necessário criar um grupo Azure AD com a identidade gerida para o seu ADF como membro. Em seguida, é necessário definir um utilizador De AD Azure como administrador ative directory para o seu servidor de base de dados Azure SQL e, em seguida, ligá-lo ao Mesmo no SQL Server Management Studio (SSMS) utilizando esse utilizador. Por fim, é necessário criar um utilizador contido que represente o grupo Azure AD, para que a identidade gerida para o seu ADF possa ser utilizada pelo Azure-SSIS IR para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Crie um grupo Azure AD com a identidade gerida para o seu ADF como membro

Pode utilizar um grupo AD Azure existente ou criar um novo utilizando o Azure AD PowerShell.

1.  Instale o módulo [PowerShell Azure AD.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Assine a `Connect-AzureAD`utilização, corra o seguinte cmdlet para criar um grupo e guarde-o numa variável:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    O resultado parece o seguinte exemplo, que também mostra o valor variável:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adicione a identidade gerida para a sua ADF ao grupo. Pode seguir o artigo [Identidade gerida para data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter o ID principal de objeto de identidade gerido (por exemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mas não utilizar o ID de aplicação de identidade gerida para este fim).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Você também pode verificar a adesão ao grupo depois.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Configure autenticação AD Azure para servidor de base de dados Azure SQL

Pode [configurar e gerir a autenticação Azure AD com a SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) utilizando os seguintes passos:

1.  No portal Azure, selecione **todos os serviços** -> **servidores SQL** da navegação à esquerda.

2.  Selecione o seu servidor de base de dados Azure SQL para ser configurado com autenticação Azure AD.

3.  Na secção **Definições** da lâmina, selecione **administração de Diretório Ativo**.

4.  Na barra de comando, selecione **Configurar**.

5.  Selecione uma conta de utilizador Azure AD para ser nomeado administrador do servidor e, em seguida, selecione **Select.**

6.  Na barra de comando, selecione **Guardar.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Criar um utilizador contido no servidor de base de dados Azure SQL que represente o grupo Azure AD

Para este próximo passo, precisa do [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Iniciar SSMS.

2. No diálogo **Connect to Server,** introduza o nome do servidor da Base de Dados Azure SQL no campo de nome do **Servidor.**

3. No campo **de Autenticação,** selecione **Ative Directory - Universal com suporte MFA** (também pode utilizar os outros dois tipos de autenticação Ative Directory, consulte Configurar e gerir a [autenticação Azure AD com SQL).](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

4. No campo de **nome do Utilizador,** introduza o nome da conta Azure testuser@xxxonline.comAD que definiu como administrador do servidor, por exemplo.

5. selecione **Ligar** e completar o processo de iniciar sessão.

6. No **Object Explorer,** expanda a pasta bases de dados do**sistema** de bases de **dados.** -> 

7. Clique à direita na base de dados **principal** e selecione **Nova consulta**.

8. Na janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   O comando deve ser concluído com sucesso, criando um utilizador contido para representar o grupo.

9. Limpe a janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   O comando deve ser concluído com sucesso, concedendo ao utilizador contido a capacidade de criar uma base de dados (SSISDB).

10. Se o seu SSISDB foi criado com a autenticação SQL e pretende mudar para utilizar a autenticação Azure AD para aceder ao seu IR Azure-SSIS, clique à direita na base de dados **SSISDB** e selecione **Nova consulta.**

11. Na janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com sucesso, criando um utilizador contido para representar o grupo.

12. Limpe a janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com sucesso, concedendo ao utilizador contido a capacidade de aceder ao SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Ativar a AD Azure na base de dados Azure SQL Managed

A Base de Dados Azure SQL Managed Instance suporta a criação de uma base de dados com a identidade gerida para o seu ADF diretamente. Não precisa de aderir à identidade gerida para o seu ADF a um grupo Azure AD nem criar um utilizador contido que represente esse grupo na sua Instância Gerida.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Configure Autenticação AD Azure para a instância gerida pela base de dados Azure SQL

Siga os passos na [Provision um administrador de Diretório Ativo Azure para a sua Instância Gerida](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adicione a identidade gerida para o seu ADF como utilizador em Caso gerido pela Base de Dados Azure SQL

Para este próximo passo, precisa do [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Iniciar SSMS.

2.  Ligue-se à sua Instância Gerida utilizando uma conta SQL Server que é uma **sisadmina**. Esta é uma limitação temporária que será removida assim que os diretores de servidores da Azure AD (logins) para a Base de Dados Azure SQL Gerida se tornarem GA. Verá o seguinte erro se tentar utilizar uma conta de administração DaD Azure para criar o login: Msg 15247, Nível 16, Estado 1, Linha 1 O utilizador não tem permissão para realizar esta ação.

3.  No **Object Explorer,** expanda a pasta bases de dados do**sistema** de bases de **dados.** -> 

4.  Clique à direita na base de dados **principal** e selecione **Nova consulta**.

5.  Na janela de consulta, execute o seguinte script T-SQL para adicionar a identidade gerida para o seu ADF como utilizador

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    O comando deve ser concluído com sucesso, concedendo a identidade gerida para a sua ADF a capacidade de criar uma base de dados (SSISDB).

6.  Se o seu SSISDB foi criado com a autenticação SQL e pretende mudar para utilizar a autenticação Azure AD para aceder ao seu IR Azure-SSIS, clique à direita na base de dados **SSISDB** e selecione **Nova consulta.**

7.  Na janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    O comando deve ser concluído com sucesso, concedendo a identidade gerida para a sua ADF a capacidade de aceder ao SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provision Azure-SSIS IR na aplicação Portal/ADF do Azure

Quando fornecer o seu IR Azure-SSIS na aplicação Portal/ADF Azure, na página **Definições SQL,** selecione Use a **autenticação AAD com a identidade gerida para a sua** opção ADF. A imagem seguinte mostra as definições para IR com o servidor de base de dados Azure SQL hospedando O SSISDB. Para o IR com instância gerida hospedar o SSISDB, o **Nível de Serviço** de Base de Dados de Catálogo e **permitir que os serviços azure acedam às** definições não são aplicáveis, enquanto outras configurações são as mesmas.

Para obter mais informações sobre como criar um IR Azure-SSIS, consulte [Create a Azure-SSIS integration runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Definições para o tempo de execução da integração Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provision Azure-SSIS IR com PowerShell

Para fornecer o seu IR Azure-SSIS com powerShell, faça as seguintes coisas:

1.  Instale o módulo  [PowerShell Azure.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)

2.  No seu guião, `CatalogAdminCredential` não coloque parâmetro. Por exemplo:

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Executar pacotes SSIS com autenticação de identidade gerida

Ao executar pacotes SSIS no Ir Azure-SSIS, pode utilizar a autenticação de identidade gerida para se ligar a vários recursos Azure. Atualmente já apoiámos a autenticação de identidade gerida nos seguintes gestores de conexão.

- [Gestor de conexão OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestor de conexão ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestor de conexão de armazenamento Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
