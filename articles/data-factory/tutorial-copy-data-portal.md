---
title: Utilize o portal Azure para criar um oleoduto de fábrica de dados
description: Este tutorial disponibiliza instruções passo-a-passo para utilizar o portal do Azure para criar uma fábrica de dados com um pipeline. O pipeline utiliza a atividade de cópia para copiar dados do armazenamento da Azure Blob para uma base de dados Azure SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 655a98ef1b6b8b2d4086b472ee7ce4d67346e5ca
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418716"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copiar dados do Armazenamento de blobs do Azure para a base de dados SQL com o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, vai criar uma fábrica de dados com a interface de utilizador (IU) do Azure Data Factory. O oleoduto desta fábrica de dados copia dados do armazenamento da Azure Blob para uma base de dados Azure SQL. O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista dos arquivos de dados suportados como origens e sinks, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Se não estiver familiarizado com o Data Factory, veja [Introdução ao Azure Data Factory](introduction.md).

Neste tutorial, vai executar os seguintes passos:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de cópia.
> * Testar a execução do pipeline.
> * Acionar o pipeline manualmente.
> * Acionar o pipeline com base numa agenda.
> * Monitorizar o pipeline e execuções de atividades.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure.** Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**. Utilize o Armazenamento de blobs como um arquivo dos dados de *origem*. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) para seguir os passos para criar uma.
* **Base de Dados Azure SQL**. Pode utilizar a base de dados como um arquivo de dados *sink*. Se não tiver uma base de dados Azure SQL, consulte [Criar uma base de dados SQL](../sql-database/sql-database-get-started-portal.md) para obter passos para criar uma.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare o Armazenamento de blobs e a Base de Dados SQL para o tutorial, ao efetuar os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como um ficheiro **emp.txt** no disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Crie um contentor com o nome **adftutorial** no Armazenamento de blobs. Crie uma pasta com o nome **input** neste contentor. Em seguida, carregue o ficheiro **emp.txt** para a pasta **input**. Utilize o portal do Azure ou ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) para realizar estas tarefas.

#### <a name="create-a-sink-sql-table"></a>Criar uma tabela SQL sink

1. Utilize o seguinte script SQL para criar a tabela **dbo.emp** na sua base de dados SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Permita que os serviços do Azure acedam ao SQL Server. Certifique-se de que **Permitir acesso aos serviços do Azure** está **ATIVADO** para o SQL Server, para que o Data Factory possa escrever dados no SQL Server. Para verificar e ligar esta definição, aceda ao servidor Azure SQL > visão geral > definir firewall do servidor> definir a opção **permitir o acesso aos serviços Do Azure** para **ON**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados.

1. Abra **o Microsoft Edge** ou o Google **Chrome.** Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. No menu esquerdo, selecione **Criar um recurso** > **Analytics** > **Data Factory**.
3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**.

   O nome da fábrica de dados Azure deve ser *globalmente único.* Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, o seu nomeADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).

     ![Nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)
4. Selecione a **subscrição** do Azure na qual pretende criar a fábrica de dados.
5. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que a fábrica de dados utiliza podem estar noutras regiões.
8. Selecione **Criar**.
9. Depois de concluída a criação, vê o aviso no Centro de Notificações. Selecione **Ir ao recurso** para navegar na página da fábrica de Dados.
10. Selecione **Criar e Monitorizar** para iniciar a IU do Data Factory num separador à parte.


## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma atividade de cópia na fábrica de dados. A atividade de cópia copia os dados do Armazenamento de blobs para a Base de Dados SQL. No [Tutorial de início rápido](quickstart-create-data-factory-portal.md), seguiu os passos abaixo para criar um pipeline:

1. Criar o serviço ligado.
1. Criar os conjuntos de dados de entrada e saída.
1. Criar um pipeline.

Neste tutorial, vai começar pela criação do pipeline. Em seguida, vai criar serviços ligados e conjuntos de dados quando forem necessários para configurar o pipeline.

1. Na página **Vamos começar**, selecione **Criar pipeline**.

   ![Criar pipeline](./media/doc-common-process/get-started-page.png)
1. No separador **Geral** do pipeline, introduza **CopyPipeline** no **Nome** do pipeline.

1. Na caixa de **ferramentas De Atividades,** expanda a categoria **Move and Transform** e arraste e deixe cair a atividade copy **Data** da caixa de ferramentas para a superfície do designer de pipeline. Especifique **CopyFromBlobToSql** em **Nome**.

    ![Atividade Copiar](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar origem

>[!TIP]
>Neste tutorial, utiliza a *chave conta* como tipo de autenticação para a sua loja de dados de origem, mas pode escolher outros métodos de autenticação suportados: *SAS URI,**Diretor de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) para obter mais detalhes.
>Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre chave Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para ilustrações detalhadas.

1. Vá ao separador **Fonte.** Selecione **+ Novo** para criar um conjunto de dados de origem.

1. Na caixa de diálogo **New Dataset,** selecione **Armazenamento De Blob Azure**, e, em seguida, selecione **Continue**. A origem de dados está num Armazenamento de blobs, pelo que vai selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem.

1. Na caixa de diálogo **Select Format,** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**.

1. Na caixa de diálogo **set Properties,** introduza **SourceBlobDataset** para nome. Selecione a caixa de verificação para **primeira linha como cabeçalho**. Na caixa de texto **de serviço Linked,** selecione **+ Novo**.

1. Na caixa de diálogo **New Linked Service (Azure Blob Storage),** introduza o **AzureStorageLinkedService** como nome, selecione a sua conta de armazenamento na lista de nomes da **conta de armazenamento.** Teste a ligação, selecione **Criar** para implementar o serviço ligado.

1. Após a criação do serviço ligado, é navegado de volta para a página de propriedades do **set.** Junto a **Caminho do ficheiro**, selecione **Procurar**.

1. Navegue para a pasta **adftutorial/entrada,** selecione o ficheiro **emp.txt** e, em seguida, selecione **OK**.

1. Selecione **OK**. Navega automaticamente para a página do oleoduto. No separador **Fonte,** confirme se **o SourceBlobDataset** está selecionado. Para pré-visualizar os dados nesta página, selecione **Pré-visualizar dados**.

    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurar sink
>[!TIP]
>Neste tutorial, utiliza a *autenticação SQL* como tipo de autenticação para a sua loja de dados de sumidouro, mas pode escolher outros métodos de autenticação suportados: *Diretor de Serviço* e Identidade *Gerida,* se necessário. Consulte as secções correspondentes [neste artigo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) para obter mais detalhes.
>Para armazenar segredos para lojas de dados de forma segura, também é recomendado usar um Cofre chave Azure. Consulte [este artigo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para ilustrações detalhadas.

1. Vá para o separador **Sink** e selecione **+ Novo** para criar um conjunto de dados sink.

1. Na caixa de diálogo **New Dataset,** insere "SQL" na caixa de pesquisa para filtrar os conectores, selecione Base de **Dados SQL Azure,** e, em seguida, selecione **Continue**. Neste tutorial, vai copiar dados para uma base de dados SQL.

1. Na caixa de diálogo **set Properties,** introduza **outputSqlDataset** para nome. A partir da lista de abandono do **serviço Linked,** selecione **+ Novo**. Os conjuntos de dados têm de estar associados a um serviço ligado. O serviço ligado tem a cadeia de ligação que o Data Factory utiliza para ligar à base de dados SQL no runtime. O conjunto de dados especifica o contentor, a pasta e o ficheiro (opcional) para os quais os dados são copiados.

1. Na caixa de diálogo New **Linked Service (Azure SQL Database),** tome os seguintes passos:

    a. Em **Name**, introduza **AzureSqlDatabaseLinkedService**.

    b. Em **Nome do servidor**, selecione a sua instância do SQL Server.

    c. Em **Nome da base de dados**, selecione a sua base de dados SQL.

    d. Em **Nome de utilizador**, introduza o nome do utilizador.

    e. Em **Palavra-passe**, introduza a palavra-passe do utilizador.

    f. Selecione **Testar ligação** para testar a ligação.

    g. Selecione **Criar** para implementar o serviço ligado.

    ![Guardar novo serviço ligado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Navega automaticamente para a caixa de diálogo **set Properties.** Em **Tabela**, selecione **[dbo].[emp]**. Em seguida, selecione **OK**.

1. Vá para o separador com o pipeline e, em **Conjunto de Dados Sink**, confirme que **OutputSqlDataset** está selecionado.

    ![Separador do pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Pode mapear opcionalmente o esquema da fonte para o esquema correspondente de destino, seguindo o [mapeamento de Schema na atividade](copy-activity-schema-and-type-mapping.md)de cópia .

## <a name="validate-the-pipeline"></a>Validar o pipeline
Para validar o pipeline, selecione **Validar** na barra de ferramentas.

Pode ver o código JSON associado ao pipeline clicando no **Código** na parte superior direita.

## <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline
Pode depurar um pipeline antes de publicar artefactos (serviços ligados, conjuntos de dados e pipeline) no Data Factory ou no seu próprio repositório Git do Azure.

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela.

1. Uma vez que o gasoduto possa funcionar com sucesso, na barra de ferramentas superior, **selecione Publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver mensagens de notificação, clique em **Mostrar Notificações**, no canto superior direito (botão do sino).

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente
Neste passo, vai acionar manualmente o pipeline que publicou no passo anterior.

1. Selecione **Acionar** na barra de ferramentas e, em seguida, selecione **Acionar Agora**. Na página **Pipeline Run,** selecione **OK**.  

1. Vá para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar links sob a coluna **PIPELINE NAME** para visualizar os detalhes da atividade e reexecutar o gasoduto.

    [![Monitorizar as corridas de gasodutos](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Para ver as operações associadas à execução do gasoduto, selecione a ligação **CopyPipeline** sob a coluna **PIPELINE NAME.** Neste exemplo, só há uma atividade, por isso só se vê uma entrada na lista. Para mais detalhes sobre o funcionamento da cópia, selecione o link **Detalhes** (ícone de óculos) sob a coluna **'NOME** DE ATIVIDADE'. Selecione **Todos os gasodutos que correm** na parte superior para voltar à vista Pipeline Runs. Para atualizar a vista, selecione **Atualizar**.

    [![Monitorizar as execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Verifique se são adicionadas mais duas linhas à tabela **emp** na base de dados SQL.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Acionar o pipeline com base numa agenda
Nesta agenda, vai criar um acionador de agenda para o pipeline. O acionador executa o pipeline na agenda especificada, como hora a hora ou diariamente. Aqui, configura o gatilho para funcionar a cada minuto até à data limite especificada.

1. Vá para o separador **Criar**, no lado esquerdo acima do separador do monitor.

1. Aceda ao seu pipeline, clique em **Acionador** na barra de ferramentas e selecione **Novo/Editar**.

1. Na caixa de diálogo **'Gatilhos' Adicionar,** selecione **+ Nova** para Escolher a área do **gatilho.**

1. Na janela **Novo Acionador**, siga os passos seguintes:

    a. Em **Nome**, introduza **RunEveryMinute**.

    b. Em **Fim**, selecione **Na Data**.

    c. Em **Fim Em**, selecione da lista pendente.

    d. Selecione a opção **dia atual**. Por predefinição, o dia de fim está definido como o dia seguinte.

    e. Atualize a parte **do tempo final** para estar alguns minutos depois da data atual. O acionador só é ativado depois de publicar as alterações. Se o definires a apenas alguns minutos de distância, e não o publicares até lá, não vês um gatilho a correr.

    f. Selecione **OK**.

    g. Para a opção **Ativada,** selecione **Sim**.

    h. Selecione **OK**.

    > [!IMPORTANT]
    > Está associado um custo a cada execução de pipeline, por isso, defina a data de fim adequadamente.

1. Na página do **gatilho Editar,** reveja o aviso e, em seguida, selecione **Guardar**. O pipeline neste exemplo não tem nenhum parâmetro.

1. Clique **em publicar tudo** para publicar o troco.

1. Vá para o separador **Monitorizar**, do lado esquerdo, para ver as execuções do pipeline acionadas.

    [![Execuções de pipeline acionadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Para mudar da vista **Pipeline Runs** para a vista **'Disparos',** selecione **Disparos** de gatilho no lado esquerdo da janela.

1. Verá as execuções do acionador numa lista.

1. Confirme que estão inseridas duas linhas por minuto (em cada execução do pipeline) na tabela **emp** até à hora de fim especificada.

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no Armazenamento de blobs. Aprendeu a:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de cópia.
> * Testar a execução do pipeline.
> * Acionar o pipeline manualmente.
> * Acionar o pipeline com base numa agenda.
> * Monitorizar o pipeline e execuções de atividades.


Avance para o tutorial seguinte, para saber como copiar dados do local para a cloud:

> [!div class="nextstepaction"]
>[Copiar dados do plano local para a cloud](tutorial-hybrid-copy-portal.md)
