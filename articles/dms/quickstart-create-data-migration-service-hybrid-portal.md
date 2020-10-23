---
title: 'Quickstart: Criar uma instância de modo híbrido com o portal Azure'
titleSuffix: Azure Database Migration Service
description: Utilize o portal Azure para criar uma instância do Serviço de Migração da Base de Dados Azure em modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: 39f893e9375970ff4bb6e3cfa2c93ceea48ec896
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331809"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Quickstart: Criar uma instância de modo híbrido com o portal Azure & Serviço de Migração da Base de Dados Azure

O modo híbrido Azure Database Migration Service gere as migrações de base de dados utilizando um trabalhador da migração que está hospedado no local juntamente com um exemplo do Serviço de Migração da Base de Dados Azure que funciona na nuvem. O modo híbrido é especialmente útil para cenários em que há falta de conectividade site-to-site entre a rede no local e a Azure ou se há uma largura de banda de conectividade site-to-site limitada.

>[!NOTE]
>Atualmente, o Serviço de Migração da Base de Dados Azure em funcionamento em modo híbrido suporta migrações do SQL Server para:
>
>- Azure SQL Gestd Instance com quase zero tempo de inatividade (online).
>- Base de dados única Azure SQL com algum tempo de inatividade (offline).
>- MongoDb para Azure CosmosDB com quase zero tempo de inatividade (online).
>- MongoDb para Azure CosmosDB com algum tempo de inatividade (offline).

Neste Quickstart, utiliza o portal Azure para criar uma instância do Serviço de Migração da Base de Dados Azure em modo híbrido. Depois, descarregue, instale e instale o trabalhador híbrido na sua rede de instalações. Durante a pré-visualização, pode utilizar o modo híbrido Azure Database Migration Service para migrar dados de uma instância no local do SQL Server para a Base de Dados Azure SQL.

> [!NOTE]
> O instalador híbrido Azure Database Migration Service funciona no Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 e Windows 10.

> [!IMPORTANT]
> O instalador híbrido Azure Database Migration Service requer .NET 4.7.2 ou mais tarde. Para encontrar as versões mais recentes de .NET, consulte a página [Quadro de Descarregamento .NET.](https://dotnet.microsoft.com/download/dotnet-framework)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser, navegue para o [portal do Microsoft Azure](https://portal.azure.com/) e, em seguida, introduza as suas credenciais para iniciar sessão no portal.

A vista predefinida é o dashboard de serviço.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registe o fornecedor de recursos Microsoft.DataMigration antes de criar a sua primeira instância do Serviço de Migração de Bases de Dados Azure.

1. No portal Azure, selecione **Subscrições,** selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Fornecedor de recursos de pesquisa](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Criar uma instância do serviço

1. Selecione +**Crie um recurso** para criar uma instância do Serviço de Migração da Base de Dados Azure.

2. Procure no Marketplace para "migração", selecione **O Serviço de Migração da Base de Dados Azure,** e, em seguida, no ecrã do **Serviço de Migração da Base de Dados Azure,** selecione **Create**.

3. No ecrã **Criar Serviço de Migração**:

    - Escolha um **Nome de Serviço** que seja memorável e único para identificar o seu caso de Serviço de Migração de Bases de Dados Azure.
    - Selecione a sua **Subscrição** do Azure, na qual pretende criar a instância.
    - Selecione um Grupo de **Recursos** existente ou crie um novo.
    - Escolha a **Localização** que esteja mais próxima do seu servidor de origem ou de destino.
    - Para **o modo serviço**, selecione Híbrido **(Pré-visualização)**.

         ![Criar serviço de migração - básicos](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecione **Rever + criar**.

5. No **separador 'Rever +' criar,** rever os Termos, verificar as outras informações fornecidas e, em seguida, selecionar **Criar**.

    ![Criar serviço de migração - Rever + criar](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Após alguns momentos, o seu exemplo do Azure Database Migration Service em modo híbrido é criado e pronto para ser configurado. A instância do Serviço de Migração da Base de Dados Azure apresenta, como mostra a seguinte imagem:

    ![Exemplo de modo híbrido do Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Após a criação do serviço, selecione **Propriedades**e, em seguida, copie o valor apresentado na caixa **de identificação** de recursos, que utilizará para instalar o trabalhador híbrido Azure Database Migration Service.

    ![Propriedades híbridas do modo híbrido do Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Criar ID de registo da App Azure

Você precisa criar um ID de registo de app Azure que o trabalhador híbrido no local pode usar para comunicar com o Azure Database Migration Service na nuvem.

1. No portal Azure, selecione **Azure Ative Directory,** selecione **registos de Aplicações**e, em seguida, selecione **Novo registo**.
2. Especifique um nome para a aplicação e, em seguida, nos **tipos de conta suportados,** selecione o tipo de contas para suportar para especificar quem pode usar a aplicação.

    ![Aplicação de registo híbrido do Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Utilize os valores predefinidos para os campos **URI (opcional) e,** em seguida, selecione **Registar**.

4. Após o registo de ID da App estar concluído, tome nota do ID da **Aplicação (cliente),** que utilizará durante a instalação do trabalhador híbrido.

5. No portal Azure, navegue para o Serviço de Migração da Base de Dados Azure, selecione **Access control (IAM)** e, em seguida, selecione **Adicionar a atribuição de funções** para atribuir o acesso do colaborador ao ID da App.

    ![Modo híbrido Azure Database Migration Service atribui função de contribuinte](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecione **o Colaborador** como função, atribua acesso ao **utilizador Azure AD ou ao principal do serviço**, e, em seguida, selecione o nome de ID da App.

    ![Modo híbrido Azure Database Migration Service atribui detalhes da função do contribuinte](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. **Selecione Guardar** para guardar a atribuição de funções para o ID da aplicação no recurso Azure Database Migration Service.

## <a name="download-and-install-the-hybrid-worker"></a>Descarregue e instale o trabalhador híbrido

1. No portal Azure, navegue para o seu exemplo do Serviço de Migração da Base de Dados Azure.

2. Em **Definições**, selecione **Híbrido**e, em seguida, selecione **Instalar download** para descarregar o trabalhador híbrido.

    ![Download híbrido do Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extraia o ficheiro ZIP no servidor que irá hospedar o trabalhador híbrido Azure Database Migration Service.

    > [!IMPORTANT]
    > O instalador híbrido Azure Database Migration Service requer .NET 4.7.2 ou mais tarde. Para encontrar as versões mais recentes de .NET, consulte a página [Quadro de Descarregamento .NET.](https://dotnet.microsoft.com/download/dotnet-framework)

4. Na pasta de instalação, localize e abra o **dmsSettings.jsno** ficheiro, especifique o **ApplicationId** e **o resourceId**e guarde o ficheiro.

    ![Azure Database Migration Service configurações híbridas do serviço de migração](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Gere um certificado que o Serviço de Migração da Base de Dados Azure pode utilizar para autenticar a comunicação do trabalhador híbrido utilizando o seguinte comando.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Um certificado é gerado na pasta Instalar.

    ![Certificado híbrido do Serviço de Migração Azure Database](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. No portal Azure, navegue para o ID da App, em **Manage,** selecione **Certificated & secrets**, e, em seguida, selecione **o certificado de upload** para selecionar o certificado público que gerou.

    ![Upload de certificado de trabalhador híbrido do Azure Database Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Instale o trabalhador híbrido Azure Database Migration Service no seu servidor no local, executando o seguinte comando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Ao executar o comando de instalação, também pode utilizar os seguintes parâmetros:
    >
    > - **-TelemetriaOptOut** - Impede o trabalhador de enviar telemetria, mas continua a registar-se localmente minimamente.  O instalador ainda envia telemetria.
    > - **-p {InstalaçãoLocalização}**. Ativa a alteração da trajetória de instalação, que por predefinição é "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Se o instalador funcionar sem erros, o serviço apresentará um estado online no Serviço de Migração da Base de Dados Azure e estará pronto para migrar as suas bases de dados.

    ![Serviço de Migração de Bases de Dados Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Desinstalar o modo híbrido do Serviço de Migração da Base de Dados de Azure

Atualmente, o modo híbrido Azure Database Migration Service é suportado apenas através do instalador híbrido Azure Database Migration Service no seu servidor no local, utilizando o seguinte comando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Ao executar o comando desinstalar, também pode utilizar o parâmetro "-ReuseCert", que mantém o certificado AdApp gerado pelo fluxo de trabalho generateCert.  Isto permite usar o mesmo certificado que foi previamente gerado e carregado.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Criar o trabalhador híbrido do Serviço de Migração da Base de Dados Azure utilizando o PowerShell

Além de instalar o trabalhador híbrido Azure Database Migration Service através do portal Azure, fornecemos um [script PowerShell](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) que pode utilizar para automatizar as etapas de instalação do trabalhador depois de criar uma nova instância do Serviço de Migração da Base de Dados Azure em modo híbrido. O script:

1. Cria um novo AdApp.
2. Descarrega o instalador.
3. Executa o fluxo de trabalho generateCert.
4. Carrega o certificado.
5. Adiciona o AdApp como contribuinte para a sua instância do Serviço de Migração da Base de Dados Azure.
6. Executa o fluxo de trabalho de instalação.

Este script destina-se a uma prototipagem rápida quando o utilizador já tiver todas as permissões necessárias no ambiente. Note que no seu ambiente de produção, o AdApp e o Cert podem ter requisitos diferentes, para que o script possa falhar.

> [!IMPORTANT]
> Este script pressupõe que existe uma instância existente do Azure Database Migration Service em modo híbrido e que a conta Azure utilizada tem permissões para criar AdApps no inquilino e modificar o Azure RBAC na subscrição.

Preencha os parâmetros no topo do script e, em seguida, execute o script a partir de uma instância PowerShell do administrador.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar o SQL Server para um Azure SQL Managed Instance online](tutorial-sql-server-managed-instance-online.md) 
>  [Migrar o SqL Server para a Base de Dados Azure SQL offline](tutorial-sql-server-to-azure-sql.md)
