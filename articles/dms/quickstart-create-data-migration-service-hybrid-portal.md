---
title: 'Quickstart: Criar uma instância de modo híbrido com portal Azure'
titleSuffix: Azure Database Migration Service
description: Utilize o portal Azure para criar uma instância do Serviço de Migração de Bases de Dados Azure em modo híbrido.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/21/2019
ms.openlocfilehash: 8dba12fc2c3777c2c45315f1805fa7ec8f1d8b83
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255047"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Quickstart: Criar uma instância de modo híbrido com o portal Azure & Azure Database Migration Service

O modo híbrido azure Database Migration Service gere as migrações de bases de dados utilizando um trabalhador migratório que está hospedado no local juntamente com uma instância do Serviço de Migração de Bases de Dados Azure que funciona na nuvem. O modo híbrido é especialmente útil para cenários em que há falta de conectividade site-a-local entre a rede no local e o Azure ou se há uma largura de banda de conectividade de site-para-local limitada.

>[!NOTE]
>Atualmente, o Serviço de Migração de Bases de Dados Azure em modo híbrido suporta as migrações do SQL Server para:
>
>- A Base de Dados Azure SQL geriu a instância com quase zero tempo de inatividade (online).
>- Base de dados Azure SQL única com algum tempo de inatividade (offline).
>- MongoDb para Azure CosmosDB com quase zero tempo de inatividade (online).
>- MongoDb para Azure CosmosDB com algum tempo de inatividade (offline).

Neste Quickstart, utiliza-se o portal Azure para criar uma instância do Serviço de Migração de Bases de Dados Azure em modo híbrido. Depois, descarrega, instala e instala o trabalhador híbrido na sua rede no local. Durante a pré-visualização, pode utilizar o modo híbrido do Serviço de Migração de Bases de Dados Azure para migrar dados de uma instância no local do SQL Server para a Base de Dados Azure SQL.

> [!NOTE]
> O instalador híbrido do Serviço de Migração de Bases de Dados Azure funciona no Microsoft Windows Server 2012 R2, Window Server 2016, Windows Server 2019 e Windows 10.

> [!IMPORTANT]
> O instalador híbrido azure Database Migration Service requer .NET 4.7.2 ou mais tarde. Para encontrar as versões mais recentes de .NET, consulte a página ['Download .NET Framework'.](https://dotnet.microsoft.com/download/dotnet-framework)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser, navegue para o [portal do Microsoft Azure](https://portal.azure.com/) e, em seguida, introduza as suas credenciais para iniciar sessão no portal.

A vista predefinida é o dashboard de serviço.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registe o fornecedor de recursos Microsoft.DataMigration antes de criar a sua primeira instância do Serviço de Migração de Bases de Dados Azure.

1. No portal Azure, selecione **Subscrições,** selecione a subscrição na qual pretende criar a instância do Serviço de Migração de Bases de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Fornecedor de recursos de pesquisa](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Criar uma instância do serviço

1. Selecione +**Crie um recurso** para criar uma instância do Serviço de Migração de Bases de Dados Azure.

2. Procure no Mercado a "migração", selecione O Serviço de Migração da Base de **Dados Azure,** e depois no ecrã do Serviço de Migração da Base de **Dados Azure,** selecione **Create**.

3. No ecrã **Criar Serviço de Migração**:

    - Escolha um **Nome de Serviço** que seja memorável e único para identificar a sua instância do Serviço de Migração de Bases de Dados Azure.
    - Selecione a sua **Subscrição** do Azure, na qual pretende criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha a **Localização** que esteja mais próxima do seu servidor de origem ou de destino.
    - Para **o modo de serviço,** selecione Hybrid **(Pré-visualização)** .

         ![Criar serviço de migração - básicos](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecione **Rever + criar**.

5. No **separador Review + criar,** rever os Termos, verificar as outras informações fornecidas e, em seguida, selecionar **Criar**.

    ![Criar serviço de migração - Rever + criar](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Após alguns momentos, a sua instância do Serviço de Migração de Bases de Dados Azure em modo híbrido é criada e pronta para ser configurada. A instância do Serviço de Migração da Base de Dados Azure apresenta-se como mostrado na seguinte imagem:

    ![Instância híbrida do Serviço de Migração de Bases de Dados Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Depois do serviço criado, selecione **Propriedades**, e, em seguida, copie o valor exibido na caixa **De si,** que utilizará para instalar o trabalhador híbrido do Serviço de Migração azure Database.

    ![Propriedades do modo híbrido do Serviço de Migração da Base de Dados Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Criar id de registo da App Azure

Você precisa criar um ID de registo azure App que o trabalhador híbrido no local pode usar para comunicar com o Azure Database Migration Service na nuvem.

1. No portal Azure, **selecione Azure Ative Directory**, selecione registos de **aplicações**e, em seguida, selecione **Nova inscrição.**
2. Especifique um nome para a aplicação e, em seguida, sob os tipos de **conta suportada,** selecione o tipo de contas para apoiar para especificar quem pode usar a aplicação.

    ![Aplicação de registo de modo híbrido do Serviço de Migração da Base de Dados Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Utilize os valores predefinidos para os campos **Redirect URI (opcional)** e, em seguida, selecione **Registar**.

4. Depois de concluído o registo de ID da App, tome nota do ID da **Aplicação (cliente),** que utilizará durante a instalação do trabalhador híbrido.

5. No portal Azure, navegue para o Serviço de Migração de Bases de Dados Azure, selecione o controlo de **acesso (IAM)** e, em seguida, selecione Adicionar a atribuição de **funções** para atribuir o acesso ao colaborador ao ID da app.

    ![Modo híbrido do Serviço de Migração da Base de Dados Azure atribui papel de contribuinte](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecione **Colaborador** como função, atribua acesso ao **utilizador da AD Azure ou ao diretor**de serviço, e, em seguida, selecione o nome de ID da aplicação.

    ![Modo híbrido do Serviço de Migração da Base de Dados Azure atribui detalhes da função do contribuinte](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecione **Guardar** para guardar a atribuição de funções para o ID da app no recurso do Serviço de Migração da Base de Dados Azure.

## <a name="download-and-install-the-hybrid-worker"></a>Descarregue e instale o trabalhador híbrido

1. No portal Azure, navegue para o seu caso de Serviço de Migração de Bases de Dados Azure.

2. Em **Definições**, selecione **Hybrid**e, em seguida, selecione **O Download do Instalador** para descarregar o trabalhador híbrido.

    ![Transferência híbrida do Serviço de Migração azure Database](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extraio o ficheiro ZIP no servidor que irá acolher o trabalhador híbrido do Serviço de Migração de Bases de Dados Azure.

    > [!IMPORTANT]
    > O instalador híbrido azure Database Migration Service requer .NET 4.7.2 ou mais tarde. Para encontrar as versões mais recentes de .NET, consulte a página ['Download .NET Framework'.](https://dotnet.microsoft.com/download/dotnet-framework)

4. Na pasta de instalação, localize e abra o ficheiro **dmsSettings.json,** especifique o **ApplicationId** e o **resourceId**e, em seguida, guarde o ficheiro.

    ![Definições híbridas do Serviço de Migração de Bases de Dados Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Gere um certificado que o Serviço de Migração da Base de Dados Azure pode utilizar para autenticar a comunicação do trabalhador híbrido utilizando o seguinte comando.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Um certificado é gerado na pasta Instalar.

    ![Certificado híbrido do Serviço de Migração da Base de Dados Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. No portal Azure, navegue para o ID da App, em **'Gerir',** selecione **Certificados e segredos,** e depois selecione **o certificado de upload** para selecionar o certificado público que gerou.

    ![Carregamento de certificado de certificado híbrido do Serviço de Migração azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Instale o trabalhador híbrido do Serviço de Migração de Bases de Dados Azure no seu servidor no local, executando o seguinte comando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

    > [!NOTE]
    > Ao executar o comando de instalação, também pode utilizar os seguintes parâmetros:
    >
    > - **-TelemettryOptOut** - Impede o trabalhador de enviar telemetria, mas continua a registar-se localmente minimamente.  O instalador ainda envia telemetria.
    > - **-p {InstalaçãoLocalização}** . Permite alterar a trajetória de instalação, que por defeito é "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Se o instalador funcionar sem erros, o serviço mostrará um estado online no Serviço de Migração de Bases de Dados Azure e está pronto para migrar as suas bases de dados.

    ![Serviço de Migração de Bases de Dados Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Desinstale o modo híbrido do Serviço de Migração da Base de Dados Azure

Atualmente, a desinstalação do modo híbrido do Serviço de Migração da Base de Dados Azure é suportado apenas através do instalador híbrido do Serviço de Migração de Bases de Dados Azure no seu servidor no local, utilizando o seguinte comando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Ao executar o comando de desinstalação, também pode utilizar o parâmetro "-ReuseCert", que mantém o cert AdApp gerado pelo fluxo de trabalho geradoCert.  Isto permite a utilização do mesmo certificado que foi anteriormente gerado e carregado.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>Criar o serviço híbrido do Serviço de Migração de Bases de Dados Azure utilizando a PowerShell

Além de instalar o serviço híbrido Azure Database Migration Service através do portal Azure, fornecemos um [script PowerShell](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) que pode utilizar para automatizar os passos de instalação do trabalhador depois de criar uma nova instância do Serviço de Migração de Bases de Dados Azure em modo híbrido. O guião:

1. Cria um novo AdApp.
2. Descarrega o instalador.
3. Executa o fluxo de trabalho geradoCert.
4. Envia o certificado.
5. Adiciona o AdApp como contribuinte à sua instância do Serviço de Migração de Bases de Dados Azure.
6. Executa o fluxo de trabalho de instalação.

Este script destina-se a uma prototipagem rápida quando o utilizador já tem todas as permissões necessárias no ambiente. Note que no seu ambiente de produção, o AdApp e o Cert podem ter requisitos diferentes, pelo que o script pode falhar.

> [!IMPORTANT]
> Este script pressupõe que existe uma instância existente do Serviço de Migração de Bases de Dados Azure em modo híbrido e que a conta Azure utilizada tem permissões para criar AdApps no inquilino e modificar o RBAC na subscrição.

Preencha os parâmetros no topo do script e, em seguida, execute o script a partir de uma instância do Administrador PowerShell.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar o SQL Server para uma Base de Dados Azure SQL gerido por
> ](tutorial-sql-server-managed-instance-online.md) migrar [o Servidor SQL para uma única base de dados ou base de dados pooled em Azure SQL Database offline](tutorial-sql-server-to-azure-sql.md)
