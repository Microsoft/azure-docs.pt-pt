---
title: Copiar dados do SQL Server para o armazenamento blob usando o portal Azure
description: Saiba como copiar dados de um arquivo de dados no local para a cloud mediante a utilização de um runtime de integração autoalojado no Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fa251b80f8e44f48de565685a0841f57f48620f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724970"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Copiar dados de uma base de dados do SQL Server para o armazenamento do Azure Blob

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste tutorial, utiliza-se a interface de utilizador da Azure Data Factory (UI) para criar um pipeline de fábrica de dados que copia dados de uma base de dados do SQL Server para o armazenamento do Azure Blob. Vai criar e utilizar um runtime de integração autoalojado, que move dados entre arquivos de dados no local e na cloud.

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do Data Factory. Para obter mais informações, veja [Introdução ao Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Subscrição do Azure
Antes de começar, se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar casos de fábrica de dados, a conta de utilizador que utiliza para iniciar sessão no Azure deve ser atribuída a uma função *de Contribuinte* ou *Proprietário* ou deve ser *administradora* da subscrição Azure.

Para ver as permissões que tem na subscrição, aceda ao portal do Azure. No canto superior direito, selecione o nome de utilizador e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de amostra sobre como adicionar um utilizador a uma função, consulte [as funções De Atribuição Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, utiliza uma base de dados SQL Server como uma loja de dados *de origem.* O pipeline na fábrica de dados que cria neste tutorial copia dados desta base de dados sql Server (fonte) para o armazenamento blob (pia). Em seguida, cria uma tabela chamada **emp** na base de dados do SQL Server e insere algumas entradas de amostras na tabela.

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado no seu computador, aceda a [Transferir o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Utilize as suas credenciais para se ligar à sua instância do SQL Server.

1. Crie uma base de dados de exemplo. Na vista de árvore, clique com o botão direito do rato em **Bases de Dados** e selecione **Nova Base de Dados**.
1. Na janela **Nova Base de Dados**, introduza um nome para a base de dados e selecione **OK**.

1. Para criar a tabela **emp** e inserir alguns dados de exemplo na mesma, execute o script de consulta seguinte na base de dados. Na vista de árvore, clique com o botão direito do rato na base de dados que criou e selecione **Nova Consulta**.

   ```
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
Neste tutorial, utiliza uma conta de armazenamento do Azure para fins gerais (mais concretamente, o Armazenamento de blobs) como arquivo de dados de destino/sink. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). O pipeline na fábrica de dados que cria neste tutorial copia dados da base de dados sql Server (fonte) para o armazenamento blob (pia). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, siga os passos seguintes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com o seu nome de utilizador e a palavra-passe do Azure.

1. No painel esquerdo, selecione **Todos os serviços**. Utilize a palavra-chave **Armazenamento** para filtrar e selecione **Contas de armazenamento**.

    ![Procurar conta de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre para a sua conta de armazenamento, se necessário. Em seguida, selecione a sua conta de armazenamento.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e cole-os no Bloco de notas ou noutro editor, para utilizar mais adiante no tutorial.

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu armazenamento de Blobs.

1. Na janela da **conta de armazenamento,** vá ao **Overview** e, em seguida, selecione **Recipientes**.

    ![Selecionar a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Recipientes,** selecione **+ Recipiente** para criar um novo.

1. Na janela **Novo contentor**, em **Nome**, introduza **adftutorial**. Em seguida, selecione **Criar**.

1. Na lista de contentores, selecione **adftutorial** que acabou de criar.

1. Mantenha a janela do **recipiente** aberta para **o adftutorial.** Usa-o para verificar a saída no final do tutorial. O Data Fabric cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados.

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. No menu esquerdo, **selecione Criar uma** Fábrica de  >  Dados de **Integração de** Recursos  >  :

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser *globalmente exclusivo*. Se vir a seguinte mensagem de erro no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

   ![Nome da nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

1. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.
        
     Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que o Data Factory utiliza podem estar noutras regiões.
1. Selecione **Criar**.

1. Depois de concluída a criação, vê a página **da Data Factory** como mostrado na imagem:

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
1. Selecione o mosaico **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.


## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página **Vamos começar**, selecione **Criar pipeline**. É criado um pipeline automaticamente por si. Verá o pipeline na vista de árvore e o respetivo editor aberto.

   ![Página Vamos começar](./media/doc-common-process/get-started-page.png)

1. No painel geral de **propriedades**, especifique **SQLServerToBlobPipeline** para **nome**. Em seguida, desabar o painel clicando no ícone Propriedades no canto superior direito.

1. Na caixa de **ferramentas Atividades,** expanda **o Move & Transform.** Arraste e largue a atividade **Copiar** para a superfície de desenho do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

1. Na janela **Propriedades**, vá para o separador **Origem** e selecione **+ Novo**.

1. Na caixa de diálogo **New Dataset,** procure **o SQL Server**. Selecione **o SQL Server** e, em seguida, selecione **Continue**.
    ![Novo conjunto de dados sqlServer](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. Na caixa de diálogo **set Properties,** em **Nome,** insira **o SqlServerDataset**. Sob **o serviço Linked**, selecione + **Novo**. Neste passo, vai criar uma ligação para o arquivo de dados de origem (base de dados do SQL Server).

1. Na caixa de diálogo **do Novo Serviço Ligado,** adicione **Nome** como **SqlServerLinkedService**. Em **Ligar através do tempo de execução da integração**, selecione **+New**.  Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O runtime de integração autoalojado é o componente que copia os dados da base de dados do SQL Server no seu computador para o armazenamento de Blobs.

1. Na caixa de diálogo de configuração de **configuração de tempo de integração,** selecione **Self-Hosted** e, em seguida, selecione **Continue**.

1. Sob o nome, **insira TutorialIntegrationRuntime**. Em seguida, selecione **Criar**.

1. Para Definições, **selecione Clique aqui para lançar a configuração expressa para este computador**. Esta ação instala o runtime de integração no seu computador e regista-o no Data Factory. Em alternativa, pode utilizar a opção de configuração manual para transferir o ficheiro de instalação, executá-lo e utilizar a chave para registar o runtime de integração.
    ![Configuração do runtime de integração](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. Na janela de **configuração express (Self-hosted) de Integração,** selecione **Fechar** quando o processo estiver concluído.

    ![Configuração rápida do runtime de integração (autoalojado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Na caixa de diálogo do **novo serviço ligado (SQL Server),** confirme que **tutorialIntegrationRuntime** é selecionado no **âmbito do Connect via tempo de execução de integração**. Em seguida, tome os seguintes passos:

    a. Em **Nome**, introduza **SqlServerLinkedService**.

    b. Em **Nome do servidor**, introduza o nome da instância do SQL Server.

    c. Em **Nome da base de dados**, introduza o nome da base de dados com a tabela **emp**.

    d. Em **Tipo de autenticação**, selecione o tipo de autenticação adequado que o Data Factory deverá utilizar para se ligar à sua base de dados do SQL Server.

    e. Em **Nome de utilizador** e **Palavra-passe**, introduza o nome de utilizador e a palavra-passe. Use *o \\ mydomain myuser* como nome de utilizador, se necessário.

    f. Selecione **Testar ligação**. Este passo é confirmar que a Data Factory pode ligar-se à base de dados do SQL Server utilizando o tempo de integração auto-hospedado que criou.

    exemplo, Para guardar o serviço ligado, selecione **Criar**.
 
    ![Novo serviço ligado (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Após a criação do serviço ligado, volta à página **de propriedades definidas** para o SqlServerDataset. Siga estes passos:

    a. Em **Serviço ligado**, confirme que vê **SqlServerLinkedService**.

    b. Em **nome de tabela,** selecione **[dbo].[ emp]**.
    
    c. Selecione **OK**.

1. Vá para o separador com **SQLServerToBlobPipeline** ou selecione **SQLServerToBlobPipeline** na vista de árvore.

1. Vá para o separador **Sink**, na parte inferior da janela **Propriedades**, e selecione **+ Novo**.

1. Na caixa de diálogo **New Dataset,** selecione **Azure Blob Storage**. Em seguida, selecione **Continuar**.

1. Na caixa de diálogo **Select Format,** escolha o tipo de formato dos seus dados. Em seguida, selecione **Continuar**.

    ![Seleção de formato de dados](./media/doc-common-process/select-data-format.png)

1. Na caixa de diálogo **set Properties,** insira **O AzureBlobDataset** para nome. Junto à caixa de texto **Serviço ligado**, selecione **+ Novo**.

1. Na caixa de diálogo **New Linked Service (Azure Blob Storage),** insira **o AzureStorageLinkedService** como nome, selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** A ligação de teste e, em seguida, **selecione Criar** para implementar o serviço ligado.

1. Após a criação do serviço ligado, volta à página **'Definir propriedades'.** Selecione **OK**.

1. Abra o conjunto de dados da pia. No separador **Ligação**, siga os passos abaixo:

    a. Em **Serviço ligado**, confirme que **AzureStorageLinkedService** está selecionado.

    b. No **caminho do ficheiro,** **introduza adftutorial/fromonprem** para a parte **do Contentor/Diretório.** Se a pasta de saída não existir no contentor adftutorial, o Data Factory cria-a automaticamente.

    c. Para a parte **'Ficheiro',** **selecione Adicionar conteúdo dinâmico**.
    ![expressão dinâmica para resolver o nome de ficheiro](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adicione `@CONCAT(pipeline().RunId, '.txt')` , e, em seguida, selecione **Acabamento**. Esta ação mudará o nome do ficheiro para PipelineRunID.txt.

1. Vá para o separador com o pipeline aberto ou selecione o pipeline na vista de árvore. Em **Conjunto de Dados de Sink**, confirme que **AzureBlobDataset** está selecionado.

1. Para validar as definições do pipeline, selecione **Validar** na barra de ferramentas do mesmo. Para fechar a **saída de validação do Tubo,** selecione o **>>** ícone.
    ![validar o gasoduto](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Para publicar as entidades que criou para a Data Factory, selecione **Publicar tudo.**

1. Espere até ver a **publicação completa** pop-up. Para verificar o estado da publicação, selecione o link **de notificações do Show** na parte superior da janela. Para fechar a janela de notificação, selecione **Fechar**.


## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline
**Selecione Adicionar Gatilho** na barra de ferramentas para a tubagem e, em seguida, selecione Trigger **Now**.

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Vá ao **separador Monitor.** Vê o gasoduto que desencadeou manualmente no passo anterior.

1. Para visualizar as operações associadas à execução do gasoduto, selecione a ligação **SQLServerToBlobPipeline** em *PIPELINE NAME*. 
    ![O gasoduto de monitorização corre](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Na página **'Atividade' executa,** selecione o link Detalhes (imagem de óculos) para ver detalhes sobre a operação de cópia. Para voltar à vista Pipeline Runs, selecione **All pipeline runs** at the top.

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída com o nome *fromonprem* no contentor de blobs `adftutorial`. Confirme que consegue ver o ficheiro *[pipeline().RunId].txt* na pasta de saída.


## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no amazenamento de Blobs. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento.
> * Criar conjuntos de dados do SQL Server e do armazenamento de Blobs.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para obter uma lista dos arquivos de dados que o Data Factory suporta, veja [Arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber como copiar dados em massa de uma origem para um destino, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)