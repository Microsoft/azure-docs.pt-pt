---
title: 'Início rápido: criar uma instância de modo híbrido com portal do Azure'
titleSuffix: Azure Database Migration Service
description: Use o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/21/2019
ms.openlocfilehash: 81039aa4aa37f9587db887beadcdb6d263ec6a74
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274817"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Início rápido: criar uma instância do modo híbrido com portal do Azure & serviço de migração de banco de dados do Azure

O modo híbrido do serviço de migração de banco de dados do Azure gerencia migrações de banco de dados usando um trabalho de migração que é hospedado localmente junto com uma instância do serviço de migração de banco de dados do Azure em execução na nuvem. O modo híbrido é especialmente útil para cenários nos quais há uma falta de conectividade site a site entre a rede local e o Azure ou se há largura de banda de conectividade site a site limitada.

>[!NOTE]
>Atualmente, o serviço de migração de banco de dados do Azure em execução no modo híbrido dá suporte a migrações de SQL Server para:
>
>- Instância gerenciada do banco de dados SQL do Azure com tempo de inatividade quase zero (online).
>- Banco de dados SQL do Azure com tempo de inatividade único (offline).
>- MongoDb para Azure CosmosDB com tempo de inatividade quase zero (online).
>- MongoDb para Azure CosmosDB com algum tempo de inatividade (offline).

Neste guia de início rápido, você usa o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido. Posteriormente, você baixa, instala e configura o Hybrid Worker em sua rede local. Durante a versão prévia, você pode usar o modo híbrido do serviço de migração de banco de dados do Azure para migrar um dado de uma instância local do SQL Server para o banco de dados SQL do Azure.

> [!NOTE]
> O instalador híbrido do serviço de migração de banco de dados do Azure é executado no Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 e Windows 10.

> [!IMPORTANT]
> O instalador híbrido do serviço de migração de banco de dados do Azure requer o .NET 4.7.2 ou posterior. Para encontrar as versões mais recentes do .NET, consulte a página [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser, navegue para o [portal do Microsoft Azure](https://portal.azure.com/) e, em seguida, introduza as suas credenciais para iniciar sessão no portal.

A vista predefinida é o dashboard de serviço.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registre o provedor de recursos Microsoft. datamigration antes de criar sua primeira instância do serviço de migração de banco de dados do Azure.

1. No portal do Azure, selecione **assinaturas**, selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Provedor de recursos de pesquisa](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Criar uma instância do serviço

1. Selecione +**criar um recurso** para criar uma instância do serviço de migração de banco de dados do Azure.

2. Pesquise "migração" no Marketplace, selecione **serviço de migração de banco de dados do Azure**e, em seguida, na tela serviço de migração de banco de **dados do Azure** , selecione **criar**.

3. No ecrã **Criar Serviço de Migração**:

    - Escolha um **nome de serviço** que seja fácil de memorizar e exclusivo para identificar sua instância do serviço de migração de banco de dados do Azure.
    - Selecione a sua **Subscrição** do Azure, na qual pretende criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha a **Localização** que esteja mais próxima do seu servidor de origem ou de destino.
    - Para **modo de serviço**, selecione **híbrido (versão prévia)** .

         ![Criar serviço de migração-noções básicas](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecione **Rever + criar**.

5. Na guia **revisar + criar** , examine os termos, verifique as outras informações fornecidas e, em seguida, selecione **criar**.

    ![Criar serviço de migração-examinar + criar](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Após alguns instantes, sua instância do serviço de migração de banco de dados do Azure no modo híbrido é criada e pronta para ser configurada. A instância do serviço de migração de banco de dados do Azure é exibida conforme mostrado na imagem a seguir:

    ![Instância do modo híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Depois que o serviço for criado, selecione **Propriedades**e, em seguida, copie o valor exibido na caixa **ID do recurso** , que você usará para instalar o trabalhador híbrido do serviço de migração de banco de dados do Azure.

    ![Propriedades do modo híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Criar Azure App ID de registro

Você precisa criar uma ID de registro de Azure App que o Hybrid Worker local possa usar para se comunicar com o serviço de migração de banco de dados do Azure na nuvem.

1. Na portal do Azure, selecione **Azure Active Directory**, selecione **registros de aplicativo**e, em seguida, selecione **novo registro**.
2. Especifique um nome para o aplicativo e, em seguida, em **tipos de conta com suporte**, selecione o tipo de contas com suporte para especificar quem pode usar o aplicativo.

    ![Aplicativo de registro modo híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Use os valores padrão para os campos **URI de redirecionamento (opcional)** e, em seguida, selecione **registrar**.

4. Após a conclusão do registro da ID do aplicativo, anote a **ID do aplicativo (cliente)** , que você usará ao instalar o Hybrid Worker.

5. No portal do Azure, navegue até serviço de migração de banco de dados do Azure, selecione **iam (controle de acesso)** e, em seguida, selecione **Adicionar atribuição de função** para atribuir acesso de colaborador à ID do aplicativo.

    ![Modo híbrido do serviço de migração de banco de dados do Azure atribuir função de colaborador](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecione **colaborador** como a função, atribua acesso ao **usuário do Azure ad ou à entidade de serviço**e, em seguida, selecione o nome da ID do aplicativo.

    ![Modo híbrido do serviço de migração de banco de dados do Azure atribuir detalhes da função de colaborador](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecione **salvar** para salvar a atribuição de função para a ID do aplicativo no recurso do serviço de migração de banco de dados do Azure.

## <a name="download-and-install-the-hybrid-worker"></a>Baixar e instalar o Hybrid Worker

1. Na portal do Azure, navegue até sua instância do serviço de migração de banco de dados do Azure.

2. Em **configurações**, selecione **híbrido**e, em seguida, selecione **download do instalador** para baixar o Hybrid Worker.

    ![Download do Hybrid Worker do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extraia o arquivo ZIP no servidor que hospedará o trabalhador híbrido do serviço de migração de banco de dados do Azure.

    > [!IMPORTANT]
    > O instalador híbrido do serviço de migração de banco de dados do Azure requer o .NET 4.7.2 ou posterior. Para encontrar as versões mais recentes do .NET, consulte a página [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) .

4. Na pasta de instalação, localize e abra o arquivo **dmsSettings. JSON** , especifique **ApplicationId** e **ResourceId**e salve o arquivo.

    ![Configurações de trabalhador híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Gere um certificado que o serviço de migração de banco de dados do Azure possa usar para autenticar a comunicação do Hybrid Worker usando o comando a seguir.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Um certificado é gerado na pasta de instalação.

    ![Certificado de trabalhador híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Na portal do Azure, navegue até a ID do aplicativo, em **gerenciar**, selecione os **segredos do & com certificado**e, em seguida, selecione **carregar certificado** para selecionar o certificado público que você gerou.

    ![Carregamento de certificado do Hybrid Worker do serviço de migração de banco de dados](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Instale o Hybrid Worker do serviço de migração de banco de dados do Azure em seu servidor local executando o seguinte comando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

    > [!NOTE]
    > Ao executar o comando instalar, você também pode usar os seguintes parâmetros:
    >
    > - **-TelemetryOptOut** -impede que o trabalho envie telemetria, mas continua a registrar localmente no mínimo.  O instalador ainda envia telemetria.
    > - **-p {InstallLocation}** . Permite alterar o caminho de instalação, que por padrão é "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Se o instalador for executado sem erros, o serviço mostrará um status online no serviço de migração de banco de dados do Azure e você estará pronto para migrar seus bancos de dados.

    ![Serviço de migração de banco de dados do Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Desinstalar o modo híbrido do serviço de migração de banco de dados do Azure

Atualmente, a desinstalação do modo híbrido do serviço de migração de banco de dados do Azure tem suporte apenas por meio do instalador do Hybrid Worker do serviço de migração de banco de dados do Azure no servidor local, usando o seguinte comando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Ao executar o comando de desinstalação, você também pode usar o parâmetro "-ReuseCert", que mantém o certificado AdApp gerado pelo fluxo de trabalho do generateCert.  Isso permite usar o mesmo certificado que foi gerado e carregado anteriormente.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Configurar o Hybrid Worker do serviço de migração de banco de dados do Azure usando o PowerShell

Além de instalar o Hybrid Worker do serviço de migração de banco de dados do Azure por meio do portal do Azure, fornecemos um [script do PowerShell](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) que você pode usar para automatizar as etapas de instalação do trabalho depois de criar uma nova instância do serviço de migração de banco de dados do Azure no modo híbrido. O script:

1. Cria um novo AdApp.
2. Baixa o instalador.
3. Executa o fluxo de trabalho generateCert.
4. Carrega o certificado.
5. Adiciona o AdApp como colaborador à sua instância do serviço de migração de banco de dados do Azure.
6. Executa o fluxo de trabalho de instalação.

Esse script destina-se a uma rápida criação de protótipos quando o usuário já tiver todas as permissões necessárias no ambiente. Observe que em seu ambiente de produção, o AdApp e o CERT podem ter requisitos diferentes, para que o script possa falhar.

> [!IMPORTANT]
> Esse script pressupõe que haja uma instância existente do serviço de migração de banco de dados do Azure no modo híbrido e que a conta do Azure usada tenha permissões para criar AdApps no locatário e para modificar o RBAC na assinatura.

Preencha os parâmetros na parte superior do script e, em seguida, execute o script de uma instância de administrador do PowerShell.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migre SQL Server para uma instância gerenciada online do banco de dados SQL do azure](tutorial-sql-server-managed-instance-online.md)
> [migre SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure offline](tutorial-sql-server-to-azure-sql.md)
