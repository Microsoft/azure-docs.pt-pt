---
title: Copiar dados do SQL Server para o armazenamento Blob usando o portal Azure
description: Saiba como copiar dados de um arquivo de dados no local para a cloud mediante a utilização de um runtime de integração autoalojado no Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 01f2644874da032b95162f3f5721ab9dbea74265
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239071"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Copiar dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Azure
Neste tutorial, vai utilizar a interface de utilizador (IU) do Azure Data Factory para criar um pipeline de fábrica de dados que copia dados de uma base de dados do SQL Server no local para o armazenamento de Blobs do Azure. Vai criar e utilizar um runtime de integração autoalojado, que move dados entre arquivos de dados no local e na cloud.

> [!NOTE]
> Este artigo não disponibiliza uma introdução detalhada do Data Factory. Para obter mais informações, veja [Introdução ao Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento do Azure.
> * Criar conjuntos de dados do SQL Server e dos Blobs do Azure.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Subscrição do Azure
Antes de começar, se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de fábricas de dados, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ter atribuída a função *Contribuidor* ou *Proprietário* ou ser *administradora* da subscrição do Azure.

Para ver as permissões que tem na subscrição, aceda ao portal do Azure. No canto superior direito, selecione o nome de utilizador e, em seguida, selecione **Permissões**. Se tiver acesso a várias subscrições, selecione a subscrição apropriada. Para obter instruções de exemplo sobre como adicionar um utilizador a uma função, veja [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, vai utilizar uma base de dados do SQL Server no local como um arquivo de dados de *origem*. O pipeline da fábrica de dados que vai criar neste tutorial copia dados desta base de dados do SQL Server no local (origem) para o Armazenamento de blobs (sink). Vai criar uma tabela com o nome **emp** na sua base de dados do SQL Server e inserir algumas entradas de exemplo na tabela.

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado no seu computador, aceda a [Transferir o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

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
Neste tutorial, utiliza uma conta de armazenamento do Azure para fins gerais (mais concretamente, o armazenamento de Blobs) como arquivo de dados de destino/sink. Se não tiver uma conta de armazenamento do Azure para fins gerais, veja [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). O pipeline da fábrica de dados que vai criar neste tutorial copia dados desta base de dados do SQL Server no local (origem) para o armazenamento de Blobs (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome e a chave da conta de armazenamento
Utilize o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, siga os passos seguintes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com o seu nome de utilizador e a palavra-passe do Azure.

1. No painel esquerdo, selecione **Todos os serviços**. Utilize a palavra-chave **Armazenamento** para filtrar e selecione **Contas de armazenamento**.

    ![Procurar conta de armazenamento](media/doc-common-process/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre para a sua conta de armazenamento, se necessário. Em seguida, selecione a sua conta de armazenamento.

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e cole-os no Bloco de notas ou noutro editor, para utilizar mais adiante no tutorial.

#### <a name="create-the-adftutorial-container"></a>Criar o contentor adftutorial
Nesta secção, vai criar um contentor de blobs com o nome **adftutorial** no seu Armazenamento de blobs.

1. Na janela **Conta de armazenamento**, vá para **Descrição Geral** e selecione **Blobs**.

    ![Selecionar a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço de Blob**, selecione **Contentor**.

1. Na janela **Novo contentor**, em **Nome**, introduza **adftutorial**. Em seguida, selecione **OK**.

1. Na lista de contentores, clique em **adftutorial**.

1. Mantenha a janela do **contentor** de **adftutorial** aberta. Vai utilizá-la para verificar o resultado no final deste tutorial. O Data Factory cria automaticamente a pasta de saída neste contentor, pelo que não precisa de a criar.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados.

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory:**

   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados tem de ser *globalmente exclusivo*. Se vir a seguinte mensagem de erro no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

   ![Nome da nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)

1. Selecione a **subscrição** do Azure na qual quer criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.
        
     Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que o Data Factory utiliza podem estar noutras regiões.
1. Selecione **Criar**.

1. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem:

    ![Home page da fábrica de dados](./media/doc-common-process/data-factory-home-page.png)
1. Selecione o mosaico **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.


## <a name="create-a-pipeline"></a>Criar um pipeline

1. Na página **Vamos começar**, selecione **Criar pipeline**. É criado um pipeline automaticamente por si. Verá o pipeline na vista de árvore e o respetivo editor aberto.

   ![Página Vamos começar](./media/doc-common-process/get-started-page.png)

1. No separador **Geral** na parte inferior da janela **Propriedades,** para **Nome,** introduza **O SQLServerToBlobPipeline**.

   ![Nome do pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Na caixa de **ferramentas De Atividades,** expanda **o Move & Transform**. Arraste e largue a atividade **Copiar** para a superfície de desenho do pipeline. Defina o nome da atividade como **CopySqlServerToAzureBlobActivity**.

1. Na janela **Propriedades**, vá para o separador **Origem** e selecione **+ Novo**.

1. Na caixa de diálogo **New Dataset,** procure o **Servidor SQL**. Selecione **O Servidor SQL**e, em seguida, selecione **Continuar**.

1. Na caixa de diálogo **set Properties,** em **nome,** introduza **SqlServerDataset**. No **serviço Linked,** selecione **+ Novo**. Neste passo, vai criar uma ligação para o arquivo de dados de origem (base de dados do SQL Server).

1. Na caixa de diálogo **new Linked Service,** adicione **o Nome** como **SqlServerLinkedService**. Em Connect através do tempo de **execução de integração,** selecione **+New**.  Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O runtime de integração autoalojado é o componente que copia os dados da base de dados do SQL Server no seu computador para o armazenamento de Blobs.

1. Na caixa de diálogo de configuração de tempo de **integração,** selecione **Self-Hosted**, e, em seguida, selecione **Next**.

1. Sob nome, **insira TutorialIntegrationRuntime**. Em seguida, selecione **Seguinte**.

1. Para definições, selecione **Clique aqui para lançar a configuração expressa para este computador**. Esta ação instala o tempo de funcionação de integração na sua máquina e regista-o na Data Factory. Em alternativa, pode utilizar a opção de configuração manual para transferir o ficheiro de instalação, executá-lo e utilizar a chave para registar o runtime de integração.

1. Na janela **Configuração Rápida do Runtime de Integração (Autoalojado)** , selecione **Fechar**.

    ![Configuração rápida do runtime de integração (autoalojado)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Na caixa de diálogo **new Linked Service,** confirme que o Tempo de Tempo de Início de Tempo de **Integração** tutorial é selecionado no Connect através do tempo de execução da **integração**. Em seguida, tome os seguintes passos:

    a. Em **Nome**, introduza **SqlServerLinkedService**.

    b. Em **Nome do servidor**, introduza o nome da instância do SQL Server.

    c. Em **Nome da base de dados**, introduza o nome da base de dados com a tabela **emp**.

    d. Em **Tipo de autenticação**, selecione o tipo de autenticação adequado que o Data Factory deverá utilizar para se ligar à sua base de dados do SQL Server.

    e. Em **Nome de utilizador** e **Palavra-passe**, introduza o nome de utilizador e a palavra-passe. Se precisar de utilizar um caráter de barra invertida (\\) no nome da sua conta de utilizador ou no nome do seu servidor, utilize o caráter de escape (\\) como prefixo. Por exemplo, utilize *omeudominion\\\\omeuutilizador*.

    f. Selecione **Testar ligação**. Este passo é confirmar que a Data Factory pode ligar-se à sua base de dados do SQL Server utilizando o tempo de integração auto-hospedado que criou.

    g. Para guardar o serviço ligado, selecione **Concluir**.

1. Deverá regressar à janela com o conjunto de dados de origem aberto. No separador **Ligação** da janela **Propriedades**, siga os passos abaixo:

    a. Em **Serviço ligado**, confirme que vê **SqlServerLinkedService**.

    b. Em **Tabela**, selecione **[dbo].[emp]** .

1. Vá para o separador com **SQLServerToBlobPipeline** ou selecione **SQLServerToBlobPipeline** na vista de árvore.

1. Vá para o separador **Sink**, na parte inferior da janela **Propriedades**, e selecione **+ Novo**.

1. Na caixa de diálogo **New Dataset,** selecione **Armazenamento De Blob Azure**. Em seguida, selecione **Continuar**.

1. Na caixa de diálogo **Select Format,** escolha o tipo de formato dos seus dados. Em seguida, selecione **Continuar**.

    ![Seleção de formato de dados](./media/doc-common-process/select-data-format.png)

1. Na caixa de diálogo **set Properties,** **introduza o Conjunto De Dados AzureBlob dataset** para nome. Junto à caixa de texto **Serviço ligado**, selecione **+ Novo**.

1. Na caixa de diálogo **New Linked Service (Azure Blob Storage),** introduza o **AzureStorageLinkedService** como nome, selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação e, em seguida, selecione **Terminar** para implementar o serviço ligado.
1. Após a criação do serviço ligado, volta à página de **propriedades do set.** Selecione **Continuar**.

1. Deverá regressar à janela com o conjunto de dados de sink aberto. No separador **Ligação**, siga os passos abaixo:

    a. Em **Serviço ligado**, confirme que **AzureStorageLinkedService** está selecionado.

    b. No **caminho do arquivo,** introduza **adftutorial/fromonprem** para a parte **contentor/diretório.** Se a pasta de saída não existir no contentor adftutorial, o Data Factory cria-a automaticamente.

    c. Para a parte **Do Ficheiro,** selecione **Adicionar conteúdo dinâmico**.
    ![expressão dinâmica para a resolução do nome do ficheiro](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adicione `@CONCAT(pipeline().RunId, '.txt')`, e, em seguida, selecione **Terminar**. Esta ação renomeará o ficheiro com PipelineRunID.txt.

1. Vá para o separador com o pipeline aberto ou selecione o pipeline na vista de árvore. Em **Conjunto de Dados de Sink**, confirme que **AzureBlobDataset** está selecionado.

1. Para validar as definições do pipeline, selecione **Validar** na barra de ferramentas do mesmo. Para fechar o **Relatório de Validação do Pipeline**, selecione **Fechar**.

1. Para publicar as entidades que criou no Data Factory, selecione **Publicar Tudo**.

1. Aguarde até ver a mensagem **Publicação com êxito**. Para verificar o estado da publicação, selecione o link **'Notificações de série'** na parte superior da janela. Para fechar a janela de notificação, selecione **Fechar**.


## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline
**Selecione Adicionar gatilho** na barra de ferramentas para o gasoduto e, em seguida, selecione **'Trigger Now**.

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Vá ao separador **Monitor.** Vê o oleoduto que desencadeou manualmente no passo anterior.

    ![Monitorizar execuções de pipeline](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Só há atividade porque só há uma atividade no oleoduto. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos), na coluna **Ações**. Para voltar à vista Pipeline Runs, selecione **Pipeline Runs** na parte superior.

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída com o nome *fromonprem* no contentor de blobs `adftutorial`. Confirme que consegue ver o ficheiro *[pipeline().RunId].txt* na pasta de saída.


## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no amazenamento de Blobs. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Crie um integration runtime autoalojado.
> * Criar serviços ligados do SQL Server e do Armazenamento.
> * Criar conjuntos de dados do SQL Server e do armazenamento de Blobs.
> * Criar um pipeline com uma atividade de cópia para mover os dados.
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Para obter uma lista dos arquivos de dados que o Data Factory suporta, veja [Arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber como copiar dados em massa de uma origem para um destino, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
