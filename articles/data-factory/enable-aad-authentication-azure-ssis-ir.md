---
title: Ativar o tempo de funcionamento da integração do Azure SSIS
description: Este artigo descreve como permitir a autenticação do Azure Ative Directory com a identidade gerida para a Azure Data Factory criar o Tempo de Execução de Integração Azure-SSIS.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392736"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Ativar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo mostra-lhe como permitir a autenticação do Azure Ative Directory (Azure AD) com a identidade gerida para a sua Azure Data Factory (ADF) e usá-la em vez de métodos de autenticação convencionais (como a autenticação SQL) para:

- Crie um Tempo de Execução de Integração Azure-SSIS (IR) que, por sua vez, providenciará uma base de dados de catálogo SSIS (SSISDB) na Base de Dados SQL ou na SQL Managed Instance em seu nome.

- Conecte-se a vários recursos Azure ao executar pacotes SSIS na Azure-SSIS IR.

Para obter mais informações sobre a identidade gerida para o seu ADF, consulte [identidade gerida para data factory.](./data-factory-service-identity.md)

> [!NOTE]
>
> - Neste cenário, a autenticação AZure AD com a identidade gerida para o seu ADF é utilizada apenas na criação e posterior operação de arranque do seu SSIS IR que, por sua vez, irá provisão e ligação ao SSISDB. Para execuções de pacotes SSIS, o seu SSIS IR ainda ligará ao SSISDB utilizando a autenticação SQL com contas totalmente geridas que são criadas durante o fornecimento do SSISDB.
> - Se já criou o seu SSIS IR utilizando a autenticação SQL, não pode reconfigurá-lo para utilizar a autenticação AD AD através do PowerShell neste momento, mas pode fazê-lo através da aplicação Azure portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Ativar AZure AD na Base de Dados Azure SQL

A SQL Database suporta a criação de uma base de dados com um utilizador AZure AD. Primeiro, você precisa criar um grupo AD Azure com a identidade gerida para o seu ADF como membro. Em seguida, tem de definir um utilizador AZure AD como administrador do Ative Directory para a sua Base de Dados SQL e, em seguida, conectá-lo no SQL Server Management Studio (SSMS) utilizando esse utilizador. Por fim, é necessário criar um utilizador contido que represente o grupo Azure AD, para que a identidade gerida para o seu ADF possa ser utilizada pela Azure-SSIS IR para criar o SSISDB em seu nome.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Crie um grupo AD AZure com a identidade gerida para o seu ADF como membro

Pode utilizar um grupo AD Azure existente ou criar um novo utilizando o Azure AD PowerShell.

1.  Instale o módulo [Azure AD PowerShell.](/powershell/azure/active-directory/install-adv2)

2.  Iniciar a `Connect-AzureAD` utilização, executar o seguinte cmdlet para criar um grupo, e guardá-lo numa variável:

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

3.  Adicione a identidade gerida para o seu ADF ao grupo. Pode seguir o artigo [Identidade gerida para](./data-factory-service-identity.md) data factory para obter o ID principal do objeto de identidade gerido (por exemplo, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, mas não utilize o ID de Aplicação de Identidade Gerida para o efeito).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Você também pode verificar a adesão ao grupo depois.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>Configurar autenticação Ad AD para base de dados SQL

Pode [configurar e gerir a autenticação Azure AD com SQL](../azure-sql/database/authentication-aad-configure.md) utilizando os seguintes passos:

1.  No portal Azure, selecione **Todos os**  ->  **servidores SQL** de serviços a partir da navegação à esquerda.

2.  Selecione o seu servidor na Base de Dados SQL para ser configurado com autenticação AD Azure.

3.  Na secção **Definições** da lâmina, selecione **Administrador Ative Directory**.

4.  Na barra de comando, selecione **Definir a administração**.

5.  Selecione uma conta de utilizador AD Azure para ser feita administradora do servidor e, em seguida, **selecione Select.**

6.  Na barra de comando, **selecione Save.**

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Criar um utilizador contido na Base de Dados SQL que representa o grupo AD AZure

Para este próximo passo, precisa do [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Inicie ssms.

2. No diálogo **'Ligar ao Servidor',** insira o nome do servidor no campo **nome do Servidor.**

3. No campo **autenticação,** selecione **Ative Directory - Universal com suporte MFA** (também pode utilizar os outros dois tipos de autenticação ative Diretório, ver [Configurar e gerir a autenticação AD AD com SQL).](../azure-sql/database/authentication-aad-configure.md)

4. No campo **nome do Utilizador,** insira o nome da conta AZure AD que definiu como administrador do servidor, por testuser@xxxonline.com exemplo.

5. **selecione Connect** e complete o processo de inscrição.

6. No **Explorador** de **Objetos,** expanda a pasta  ->  **bases de dados do sistema de bases de dados.**

7. Clique com o botão direito na base de dados **principal** e selecione **Nova consulta**.

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

10. Se o seu SSISDB foi criado utilizando a autenticação SQL e pretende mudar para utilizar a autenticação AZure AD para o seu Azure-SSIS IR aceder ao mesmo, certifique-se primeiro de que os passos para conceder permissão à base de dados **principal** terminaram com sucesso. Em seguida, clique com o botão direito na base de **dados SSISDB** e selecione **Nova consulta**.

11. Na janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com sucesso, criando um utilizador contido para representar o grupo.

12. Limpe a janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    O comando deve ser concluído com sucesso, garantindo ao utilizador contido a capacidade de aceder ao SSISDB.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>Ativar Azure AD em SQL Gestditutura Instância

SQL Managed Instance suporta a criação de uma base de dados com a identidade gerida para o seu ADF diretamente. Não precisa de se juntar à identidade gerida para o seu ADF a um grupo AD Azure nem criar um utilizador contido que represente esse grupo em SQL Managed Instance.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Configurar a autenticação AD AD para Azure SQL Gestded Instance

Siga os passos em [Provision a Azure Ative Directory administrador para a SQL Managed Instance](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>Adicione a identidade gerida para o seu ADF como utilizador em SQL Managed Instance

Para este próximo passo, precisa do [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Inicie ssms.

2.  Conecte-se a SQL Managed Instance usando uma conta SQL Server que é um **sysadmin**. Esta é uma limitação temporária que será removida assim que os principais do servidor AZure AD (logins) para Azure SQL Managed Instance se tornarem GA. Verá o seguinte erro se tentar utilizar uma conta de administração Azure AD para criar o login: Msg 15247, Nível 16, Estado 1, Linha 1 O utilizador não tem permissão para realizar esta ação.

3.  No **Explorador** de **Objetos,** expanda a pasta  ->  **bases de dados do sistema de bases de dados.**

4.  Clique com o botão direito na base de dados **principal** e selecione **Nova consulta**.

5.  Na janela de consulta, execute o seguinte script T-SQL para adicionar a identidade gerida para o seu ADF como utilizador

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    O comando deverá ser concluído com sucesso, concedendo à identidade gerida para o seu ADF a capacidade de criar uma base de dados (SSISDB).

6.  Se o seu SSISDB foi criado utilizando a autenticação SQL e pretende mudar para utilizar a autenticação AZure AD para o seu Azure-SSIS IR aceder ao mesmo, certifique-se primeiro de que os passos para conceder permissão à base de dados **principal** terminaram com sucesso. Em seguida, clique com o botão direito na base de **dados SSISDB** e selecione **Nova consulta**.

7.  Na janela de consulta, introduza o seguinte comando T-SQL e selecione **Executar** na barra de ferramentas.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    O comando deve ser concluído com sucesso, concedendo à identidade gerida para o seu ADF a capacidade de aceder ao SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Provision Azure-SSIS IR in Azure portal/ADF app

Quando forte a sua Azure-SSIS IR na aplicação Azure portal/ADF, na página **SQL Settings,** selecione **Use AAD autenticação com a identidade gerida para a sua** opção ADF. A imagem que se segue mostra as definições de IR com a SQL Database hospedando SSISDB. Para o IR com SQL Managed Instance hospedando SSISDB, o **Nível de Serviço de Base de Dados de Catálogo** e **permitir que os serviços Azure acedam às** definições não são aplicáveis, enquanto outras configurações são as mesmas.

Para obter mais informações sobre como criar um Azure-SSIS IR, consulte [Criar um tempo de integração Azure-SSIS na Azure Data Factory](./create-azure-ssis-integration-runtime.md).

![Definições para o tempo de integração Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Provision Azure-SSIS IR com PowerShell

Para forir o seu Azure-SSIS IR com PowerShell, faça as seguintes coisas:

1.  Instale o módulo [Azure PowerShell.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)

2.  No seu script, não desateia `CatalogAdminCredential` o parâmetro. Por exemplo:

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

Quando executar pacotes SSIS no Azure-SSIS IR, pode utilizar a autenticação de identidade gerida para ligar a vários recursos Azure. Atualmente já apoiamos a autenticação de identidade gerida nos seguintes gestores de ligação.

- [Gestor de Conexão OLE DB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestor de Conexão ADO.NET](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestor de conexão de armazenamento Azure](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
