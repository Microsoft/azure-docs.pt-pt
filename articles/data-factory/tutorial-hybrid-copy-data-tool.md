---
title: Copiar dados no local com a ferramenta Copiar Dados do Azure | Microsoft Docs
description: Crie uma fábrica de dados do Azure e utilize a ferramenta Copiar Dados para copiar dados de uma base de dados do SQL Server no local para um armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 3569d39b8e4668894e44507dfdd0e20297028290
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226509"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copiar dados de uma base de dados do SQL Server no local para o Armazenamento de blobs do Azure com a ferramenta Copiar Dados
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-hybrid-copy-data-tool.md)

Neste tutorial, irá utilizar o portal do Azure para criar uma fábrica de dados. Utilize a ferramenta Copiar Dados para criar um pipeline que copia dados de uma base de dados SQL Server no local para o Armazenamento de blobs do Azure.

> [!NOTE]
> - Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Subscrição do Azure
Antes de começar, se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de fábricas de dados, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ter atribuída a função *Contribuidor* ou *Proprietário* ou ser *administradora* da subscrição do Azure. 

Para ver as permissões que tem na subscrição, aceda ao portal do Azure. Selecione o nome de utilizador no canto superior direito, e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de *origem*. O pipeline da fábrica de dados que vai criar neste tutorial copia dados desta base de dados do SQL Server no local (origem) para o Armazenamento de blobs (sink). Vai criar uma tabela com o nome **emp** na sua base de dados do SQL Server e inserir algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado no seu computador, aceda a [Transferir o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Utilize as suas credenciais para se ligar à sua instância do SQL Server. 

1. Crie uma base de dados de exemplo. Na vista de árvore, clique com o botão direito do rato em **Bases de Dados** e selecione **Nova Base de Dados**. 

1. Na janela **Nova Base de Dados**, introduza um nome para a base de dados e selecione **OK**. 

1. Para criar a tabela **emp** e inserir alguns dados de exemplo na mesma, execute o script de consulta seguinte na base de dados. Na vista de árvore, clique com o botão direito do rato na base de dados que criou e selecione **Nova Consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Neste tutorial, utiliza uma conta de armazenamento do Azure para fins gerais (mais concretamente, o Armazenamento de blobs) como arquivo de dados de destino/sink. Se não tiver uma conta de armazenamento para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para instruções sobre como criar uma. O pipeline da fábrica de dados que vai criar neste tutorial copia dados da base de dados do SQL Server no local (origem) para este Armazenamento de blobs (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, siga os passos seguintes: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com o seu nome de utilizador e a palavra-passe do Azure. 

1. No painel esquerdo, selecione **Mais serviços**. Utilize a palavra-chave **Armazenamento** para filtrar e selecione **Contas de armazenamento**.

    ![Procurar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre para encontrar a sua conta de armazenamento, se necessário. Em seguida, selecione a sua conta de armazenamento. 

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Chaves de acesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e cole-os no Bloco de notas ou noutro editor, para utilizar mais adiante no tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial 
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu Armazenamento de blobs. 

1. Na janela **Conta de armazenamento**, mude para a **Descrição Geral** e selecione **Blobs**. 

    ![Selecionar a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço de Blob**, selecione **Contentor**. 

    ![Botão de contentor](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Na janela **Novo Contentor**, na caixa **Nome**, introduza **adftutorial** e selecione **OK**. 

    ![Novo contentor](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Na lista de contentores, clique em **adftutorial**.

    ![Seleção de contentor](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Mantenha a janela **Contentor** de **adftutorial** aberta. Vai utilizá-la para verificar o resultado no final deste tutorial. O Data Factory cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar.

    ![Janela do contentor](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. No menu da esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**. 
  
   ![Criação de nova fábrica de dados](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
   
     ![Nova fábrica de dados](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)

   O nome da fábrica de dados tem de ser *globalmente exclusivo*. Se vir a seguinte mensagem de erro no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

   ![Nome da nova fábrica de dados](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
1. Selecione a **subscrição** do Azure na qual quer criar a fábrica de dados. 
1. Em **Grupo de Recursos**, efetue um destes passos:
  
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

   - Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
        
     Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que o Data Factory utiliza podem estar noutras localizações/regiões.
1. Selecione **Afixar ao dashboard**. 
1. Selecione **Criar**.
1. No dashboard, é apresentado o mosaico seguinte, com o estado **A Implementar o Data Factory**:

    ![Mosaico A Implementar o Data Factory](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
1. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
  
     ![Home page da fábrica de dados](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
1. Selecione **Criar e Monitorizar** para iniciar a interface de utilizador do Data Factory num separador à parte. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Utilizar a ferramenta Copiar Dados para criar um pipeline

1. Na página **Vamos começar**, selecione a página **Copiar Dados** para iniciar a ferramenta Copiar Dados. 

   ![Mosaico ferramenta Copiar Dados](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)

1. Na página **Propriedades** da ferramenta Copiar Dados, em **Nome da tarefa**, introduza **CopyFromOnPremSqlToAzureBlobPipeline**. Em seguida, selecione **Seguinte**. A ferramenta Copiar Dados cria um pipeline com o nome que especificar para este campo. 

   ![Nome da tarefa](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na página **Arquivo de dados de origem**, clique em **Criar nova ligação**. 

   ![Criar novo serviço ligado](./media/tutorial-hybrid-copy-data-tool/create-new-source-data-store.png)

1. Em **Novo Serviço Ligado**, procure o **SQL Server** e, em seguida, selecione **Seguinte**. 

   ![Seleção do SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)

1. No novo serviço ligado (SQL Server) **Name**, introduza **SqlServerLinkedService**. Selecione **+Novo** em **Ligar através do runtime de integração**. Tem de criar um runtime de integração autoalojado, transferi-lo para o seu computador e registá-lo no Data Factory. O runtime de integração autoalojado copia dados entre o ambiente no local e a cloud.

   ![Criar o runtime de integração autoalojado](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)

1. Na caixa de diálogo **Configuração do Integration Runtime**, Selecione **Rede Privada**. Em seguida, selecione **Seguinte**. 

   ![](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. Na caixa de diálogo **Configurar Integration Runtime**, em **Nome**, introduza **TutorialIntegrationRuntime**. Em seguida, selecione **Seguinte**. 

   ![Nome do runtime de Integração](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)

1. Selecione **Clique aqui para iniciar a configuração rápida neste computador**. Esta ação instala o runtime de integração no seu computador e regista-o no Data Factory. Em alternativa, pode utilizar a opção de configuração manual para transferir o ficheiro de instalação, executá-lo e utilizar a chave para registar o runtime de integração. 

    ![Ligação para iniciar a configuração rápida neste computador](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)

1. Execute a aplicação transferida. Pode ver o estado da configuração rápida na janela. 

    ![Estado da configuração rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Confirme que **TutorialIntegrationRuntime** está selecionado para o campo **Integration Runtime**.

      ![Runtime de integração selecionado](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Em **Especificar a base de dados do SQL Server no local**, siga os passos seguintes: 

    a. Em **Nome**, introduza **SqlServerLinkedService**.

    b. Em **Nome do servidor**, introduza o nome da instância do SQL Server no local.

    c. Em **Nome da base de dados**, introduza o nome da base de dados no local.

    d. Em **Tipo de autenticação**, selecione a autenticação adequada.

    e. Em **Nome de utilizador**, introduza o nome do utilizador com acesso ao SQL Server no local.

    f. Introduza a **palavra-passe** do utilizador. Selecione **Concluir**. 

1. Selecione **Seguinte**.

       ![](./media/tutorial-hybrid-copy-data-tool/select-source-linked-service.png)

1. Na página **Selecionar tabelas a partir das quais copiar dados ou utilizar uma consulta personalizada**, selecione a tabela **[dbo].[emp]** da lista e selecione **Seguinte**. Pode selecionar qualquer outra tabela com base na sua base de dados.

       ![The Product table selection](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)

1. Na página **Arquivo de dados de destino**, selecione **Criar nova ligação**

       //image create-new-sink-connection.png

       ![Create Destination linked service](./media/tutorial-hybrid-copy-data-tool/create-new-sink-connection.png)

1. No **Novo Serviço Ligado**, Procure e Selecione **Blob do Azure** e, em seguida, **Continuar**. 

       ![Blob storage selection](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Na caixa de diálogo **Novo Serviço Ligado (Armazenamento de Blobs do Azure)**, siga os passos seguintes: 

       a. Under **Name****, enter **AzureStorageLinkedService**.

       b. Under **Connect via integration runtime**, select **TutorialIntegrationRuntime**

       c. Under **Storage account name**, select your storage account from the drop-down list. 

       d. Select **Next**.

       ![Specify the storage account](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)

1. Na caixa de diálogo **Arquivo de dados de destino**, selecione **Seguinte**. Nas **Propriedades de ligação**, selecione **Serviço de armazenamento do Azure** como **Armazenamento de Blobs do Azure**. Selecione **Seguinte**. 

       ![connection properties](./media/tutorial-hybrid-copy-data-tool/select-connection-properties.png)

1. Na caixa de diálogo **Escolher ficheiro ou pasta de saída**, em **Caminho da pasta**, introduza **adftutorial/fromonprem**. Criou o contentor **adftutorial** como parte dos pré-requisitos. Se a pasta de saída não existir (neste caso **fromonprem**), o Data Factory cria-a automaticamente. Também pode utilizar o botão **Procurar** para procurar no Armazenamento de blobs e nos respetivos contentores/pastas. Se não especificar qualquer valor em **Nome de ficheiro**, por predefinição o nome da origem deverá ser utilizado (neste caso **dbo.emp**).
           
       ![Choose the output file or folder](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Na caixa de diálogo **Definições do formato do ficheiro**, selecione **Seguinte**. 

       ![File format settings page](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)

1. Na caixa de diálogo **Definições**, selecione **Seguinte**. 

       ![Settings page](./media/tutorial-hybrid-copy-data-tool/settings-page.png)

1. Na caixa de diálogo **Resumo**, reveja os valores para todas definições e selecione **Seguinte**. 

       ![Summary page](./media/tutorial-hybrid-copy-data-tool/summary-page.png)

1. Na página **Implementação**, selecione **Monitorização** para monitorizar o pipeline ou a tarefa que criou.

       ![Deployment page](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. No separador **Monitorização**, pode ver o estado do pipeline que criou. Pode utilizar as ligações na coluna **Ações** para ver as execuções de atividades associadas à execução do pipeline e voltar a executar o pipeline. 

       ![Monitor pipeline runs](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)

1. Selecione a ligação **Ver Execuções de Atividade** na coluna **Ações** para ver as execuções de atividades associadas à execução do pipeline. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. Para regressar à vista **Execuções do Pipeline**, selecione **Pipelines** na parte superior.

       ![Monitor activity runs](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)

1. Confirme que vê um ficheiro de saída na pasta **fromonprem** do contentor **adftutorial**. 

       ![Output blob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)

1. Selecione o separador **Editar** à esquerda para mudar para o modo de edição. Pode atualizar os serviços ligados, conjuntos de dados e pipelines criados pela ferramenta, utilizando o editor. Selecione **Código** para ver o código JSON associado à entidade aberta no editor. Para obter detalhes sobre como editar estas entidades na IU do Data Factory, veja [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

       ![Edit tab](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia os dados de uma base de dados do SQL Server no local para o Armazenamento de blobs. Aprendeu a: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Utilizar a ferramenta Copiar Dados para criar um pipeline.
> * Monitorizar o pipeline e execuções de atividades.

Para obter uma lista dos arquivos de dados que o Data Factory suporta, veja [Arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber como copiar dados em massa de uma origem para um destino, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
