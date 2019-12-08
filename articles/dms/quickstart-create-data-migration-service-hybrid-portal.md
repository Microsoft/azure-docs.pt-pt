---
title: 'Início rápido: criar uma instância do modo híbrido do serviço de migração de banco de dados do Azure usando o portal do Azure | Microsoft Docs'
description: Usar o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 12/06/2019
ms.openlocfilehash: a124c33f15318f1b9b22a750a1de15601823afa3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890696"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Início rápido: criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido usando o portal do Azure (versão prévia)

O modo híbrido do serviço de migração de banco de dados do Azure gerencia migrações de banco de dados usando um trabalho de migração que é hospedado localmente junto com uma instância do serviço de migração de banco de dados do Azure em execução na nuvem. O modo híbrido é especialmente útil para cenários nos quais há uma falta de conectividade site a site entre a rede local e o Azure ou se há largura de banda de conectividade site a site limitada.

Neste guia de início rápido, você usa o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido. Posteriormente, você baixa, instala e configura o Hybrid Worker em sua rede local. Durante a versão prévia, você pode usar o modo híbrido do serviço de migração de banco de dados do Azure para migrar um dado de uma instância local do SQL Server para o banco de dados SQL do Azure.

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

2. Procure "migração" no marketplace, selecione **Azure Database Migration Service** e, em seguida, no ecrã **Azure Database Migration Service** selecione **Criar**.

3. No ecrã **Criar Serviço de Migração**:

    - Escolha um **nome de serviço** que seja fácil de memorizar e exclusivo para identificar sua instância do serviço de migração de banco de dados do Azure.
    - Selecione a sua **Subscrição** do Azure, na qual pretende criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha a **Localização** que esteja mais próxima do seu servidor de origem ou de destino.

    > [!IMPORTANT]
    > Durante a visualização, há suporte para o modo híbrido apenas na região leste dos EUA. Como o Hybrid Worker é instalado em sua rede local, há pouco ou nenhum impacto no desempenho, mesmo se você estiver migrando para um destino em uma região diferente.

    - Para **modo de serviço**, selecione **híbrido (versão prévia)** .

      ![Criar serviço de migração-noções básicas](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecione **Rever + criar**.

5. Na guia **revisar + criar** , examine os termos, verifique as outras informações fornecidas e, em seguida, selecione **criar**.

    ![Criar serviço de migração-examinar + criar](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Após alguns instantes, sua instância do serviço de migração de banco de dados do Azure no modo híbrido é criada e pronta para ser configurada. A instância do serviço de migração de banco de dados do Azure é exibida conforme mostrado na imagem a seguir:

    ![Instância do modo híbrido do serviço de migração de banco de dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Após a criação do serviço, selecione **Propriedades**e copie o valor exibido na caixa **ID do recurso** , que você usará para instalar o trabalhador híbrido do serviço de migração de banco de dados do Azure.

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

8. Se o instalador for executado sem erros, o serviço mostrará um status online no serviço de migração de banco de dados do Azure e você estará pronto para migrar seus bancos de dados.

    ![Serviço de migração de banco de dados do Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Desinstalar o modo híbrido do serviço de migração de banco de dados do Azure

Atualmente, a desinstalação do modo híbrido do serviço de migração de banco de dados do Azure tem suporte apenas por meio do instalador do Hybrid Worker do serviço de migração de banco de dados do Azure no servidor local, usando o seguinte comando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migre SQL Server para uma instância gerenciada online do banco de dados SQL do azure](tutorial-sql-server-managed-instance-online.md)
> [migre SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure offline](tutorial-sql-server-to-azure-sql.md)
