---
title: Utilizar o portal do Azure para criar um pipeline de fábrica de dados | Microsoft Docs
description: Este tutorial disponibiliza instruções passo-a-passo para utilizar o portal do Azure para criar uma fábrica de dados com um pipeline. O pipeline utiliza a atividade para copiar os dados de um Armazenamento de blobs do Azure para uma base de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: a1a97e7521e9772b1d13049d42e67d7ac318fffc
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907367"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Copiar dados do Armazenamento de blobs do Azure para a base de dados SQL com o Azure Data Factory
Neste tutorial, vai criar uma fábrica de dados com a interface de utilizador (IU) do Azure Data Factory. O pipeline nesta fábrica de dados copia os dados do Armazenamento de blobs do Azure para uma base de dados SQL. O padrão de configuração neste tutorial aplica-se à cópia a partir de um arquivo de dados baseado em ficheiros para um arquivo de dados relacional. Para obter uma lista dos arquivos de dados suportados como origens e sinks, consulte a tabela de [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

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
* **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de armazenamento do Azure**. Utilize o Armazenamento de blobs como um arquivo dos dados de *origem*. Se não tiver uma conta de armazenamento, veja [Criar uma conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md) para seguir os passos para criar uma.
* **Base de Dados SQL do Azure**. Pode utilizar a base de dados como um arquivo de dados *sink*. Se não tiver uma base de dados SQL, veja [Criar uma base de dados SQL](../sql-database/sql-database-get-started-portal.md) para seguir os passos para criar uma.

### <a name="create-a-blob-and-a-sql-table"></a>Criar um blob e uma tabela SQL

Agora, prepare o Armazenamento de blobs e a Base de Dados SQL para o tutorial, ao efetuar os seguintes passos.

#### <a name="create-a-source-blob"></a>Criar um blob de origem

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como um ficheiro **emp.txt** no disco:

    ```
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

1. Permita que os serviços do Azure acedam ao SQL Server. Certifique-se de que **Permitir acesso aos serviços do Azure** está **ATIVADO** para o SQL Server, para que o Data Factory possa escrever dados no SQL Server. Para verificar e ativar essa configuração, vá para Azure SQL Server > Visão geral > definir Firewall do servidor > defina a opção **permitir acesso aos serviços do Azure** como **ativado**.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai criar uma fábrica de dados e iniciar a IU do Data Factory para criar um pipeline na fábrica de dados. 

1. Abra **o Microsoft Edge ou o** **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. No menu à esquerda, selecione **criar um recurso** > **análise** > **Data Factory**: 
  
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Na página **Nova fábrica de dados**, em **Nome**, introduza **ADFTutorialDataFactory**. 
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se receber uma mensagem de erro relacionada com o valor do nome, introduza um nome diferente para a fábrica de dados. (por exemplo, yournameADFTutorialDataFactory). Para obter as regras de nomenclatura dos artefactos do Data Factory, veja [Regras de nomenclatura do Data Factory](naming-rules.md).
        
     ![Nova fábrica de dados](./media/doc-common-process/name-not-available-error.png)
4. Selecione a **subscrição** do Azure na qual quer criar a fábrica de dados. 
5. Em **Grupo de Recursos**, efetue um destes passos:
     
    a. Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente.

    b. Selecione **Criar novo** e introduza o nome de um grupo de recursos. 
         
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
6. Em **Versão**, selecione **V2**.
7. Em **Localização**, selecione uma localização para a fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (por exemplo, o Armazenamento do Azure e a Base de Dados SQL) e as computações (por exemplo, o Azure HDInsight) que a fábrica de dados utiliza podem estar noutras regiões.
8. Selecione **Criar**. 
9. Depois que a criação for concluída, você verá o aviso no centro de notificações. Selecione **ir para o recurso** para navegar até a página data Factory.
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

1. Na caixa de ferramentas **atividades** , expanda a categoria **mover e transformar** e arraste e solte a atividade **copiar dados** da caixa de ferramentas para a superfície do designer de pipeline. Especifique **CopyFromBlobToSql** em **Nome**.

    ![Atividade Copiar](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurar origem

1. Vá para o separador **Origem**. Selecione **+ Novo** para criar um conjunto de dados de origem. 

1. Na caixa de diálogo **novo conjunto de novos** , selecione **armazenamento de BLOBs do Azure**e, em seguida, selecione **continuar**. A origem de dados está num Armazenamento de blobs, pelo que vai selecionar o **Armazenamento de Blobs do Azure** para o conjunto de dados de origem. 

1. Na caixa de diálogo **selecionar formato** , escolha o tipo de formato dos dados e, em seguida, selecione **continuar**.

    ![Tipo de formato de dados](./media/doc-common-process/select-data-format.png)

1. Na caixa de diálogo **definir propriedades** , digite **SourceBlobDataset** para o nome. Junto à caixa de texto **Serviço ligado**, selecione **+ Novo**. 
    
1. Na caixa de diálogo **novo serviço vinculado (armazenamento de BLOBs do Azure)** , digite **AzureStorageLinkedService** como nome, selecione sua conta de armazenamento na lista **nome da conta de armazenamento** . Teste a conexão e selecione **concluir** para implantar o serviço vinculado.

1. Depois que o serviço vinculado é criado, ele é navegado de volta para a página **definir propriedades** . Junto a **Caminho do ficheiro**, selecione **Procurar**.

1. Navegue para a pasta **adftutorial/input**, selecione o ficheiro **emp.txt** e, em seguida, selecione **Concluir**.

1. Ele navega automaticamente para a página de pipeline. Na guia **origem** , confirme se **SourceBlobDataset** está selecionado. Para pré-visualizar os dados nesta página, selecione **Pré-visualizar dados**. 
    
    ![Conjunto de dados de origem](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Configurar sink

1. Vá para o separador **Sink** e selecione **+ Novo** para criar um conjunto de dados sink. 

1. Na caixa de diálogo **novo conjunto** de dados, insira "SQL" na caixa de pesquisa para filtrar os conectores, selecione **banco de dados SQL do Azure**e, em seguida, selecione **continuar**. Neste tutorial, vai copiar dados para uma base de dados SQL. 

1. Na caixa de diálogo **definir propriedades** , digite **OutputSqlDataset** para o nome. Junto à caixa de texto **Serviço ligado**, selecione **+ Novo**. Os conjuntos de dados têm de estar associados a um serviço ligado. O serviço ligado tem a cadeia de ligação que o Data Factory utiliza para ligar à base de dados SQL em runtime. O conjunto de dados especifica o contentor, a pasta e o ficheiro (opcional) para os quais os dados são copiados. 
      
1. Na caixa de diálogo **novo serviço vinculado (banco de dados SQL do Azure)** , execute as seguintes etapas: 

    a. Em **Name**, introduza **AzureSqlDatabaseLinkedService**.

    b. Em **Nome do servidor**, selecione a sua instância de SQL Server.

    c. Em **Nome da base de dados**, selecione a sua base de dados SQL.

    d. Em **Nome de utilizador**, introduza o nome do utilizador.

    e. Em **Palavra-passe**, introduza a palavra-passe do utilizador.

    f. Selecione **Testar ligação** para testar a ligação.

    g. Selecione **concluir** para implantar o serviço vinculado. 
    
    ![Guardar novo serviço ligado](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Ele navega automaticamente para a caixa de diálogo **definir propriedades** . Em **Tabela**, selecione **[dbo].[emp]** . Em seguida, selecione **Concluir**.

1. Vá para o separador com o pipeline e, em **Conjunto de Dados Sink**, confirme que **OutputSqlDataset** está selecionado.

    ![Separador do pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Opcionalmente, você pode mapear o esquema da origem para o esquema correspondente de destino, seguindo o [mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md)
    
## <a name="validate-the-pipeline"></a>Validar o pipeline
Para validar o pipeline, selecione **Validar** na barra de ferramentas.
 
Você pode ver o código JSON associado ao pipeline clicando em **código** no canto superior direito.

## <a name="debug-and-publish-the-pipeline"></a>Depurar e publicar o pipeline
Pode depurar um pipeline antes de publicar artefactos (serviços ligados, conjuntos de dados e pipeline) no Data Factory ou no seu próprio repositório Git do Azure. 

1. Para depurar o pipeline, selecione **Depurar** na barra de ferramentas. Verá o estado da execução do pipeline no separador **Saída**, na parte inferior da janela. 

1. Depois que o pipeline puder ser executado com êxito, na barra de ferramentas superior, selecione **publicar tudo**. Esta ação publica as entidades (conjuntos de dados e pipeline) que criou no Data Factory.

1. Aguarde até ver a mensagem **Publicação com êxito**. Para ver mensagens de notificação, clique em **Mostrar Notificações**, no canto superior direito (botão do sino). 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente
Neste passo, vai acionar manualmente o pipeline que publicou no passo anterior. 

1. Selecione **Adicionar gatilho** na barra de ferramentas e, em seguida, selecione **disparar agora**. Na página **Executar Pipeline**, selecione **Concluir**.  

1. Vá para o separador **Monitorizar**, no lado esquerdo. Verá uma execução de pipeline que é acionada por um acionador manual. Pode utilizar as ligações na coluna **Ações** para ver os detalhes das atividades e para voltar a executar o pipeline.

    ![Monitorizar execuções de pipeline](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Para ver as execuções de atividade associadas à execução do pipeline, selecione a ligação **Ver Execuções de Atividade** na coluna **Ações**. Neste exemplo, há apenas uma atividade, portanto, você vê apenas uma entrada na lista. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (ícone de óculos) na coluna **Ações**. Selecione **pipeline executions** na parte superior para voltar para o modo de exibição de execuções de pipeline. Para atualizar a vista, selecione **Atualizar**.

    ![Monitorização de execuções de atividade](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Verifique se são adicionadas mais duas linhas à tabela **emp** na base de dados SQL. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Acionar o pipeline com base numa agenda
Nesta agenda, vai criar um acionador de agenda para o pipeline. O acionador executa o pipeline na agenda especificada, como hora a hora ou diariamente. Aqui, você define o gatilho para ser executado a cada minuto até o DateTime final especificado. 

1. Vá para o separador **Criar**, no lado esquerdo acima do separador do monitor. 

1. Vá para seu pipeline, clique em **Adicionar gatilho** na barra de ferramentas e selecione **novo/editar**. 

1. Na caixa de diálogo **Adicionar gatilhos** , selecione **+ novo** para escolher área de **gatilho** .

1. Na janela **Novo Acionador**, siga os passos seguintes: 

    a. Em **Nome**, introduza **RunEveryMinute**.

    b. Em **Fim**, selecione **Na Data**.

    c. Em **Fim Em**, selecione da lista pendente.

    d. Selecione a opção **dia atual**. Por predefinição, o dia de fim está definido como o dia seguinte.

    e. Atualize a parte da **hora de término** para que ela tenha alguns minutos após a data e hora atual. O acionador só é ativado depois de publicar as alterações. Se você defini-lo como apenas alguns minutos de distância e não o publicar, não verá uma execução de gatilho.

    f. Selecione **Aplicar**. 

    g. Para a opção **ativado** , selecione **Sim**. 

    h. Selecione **Seguinte**.

    ![Botão Ativado](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Está associado um custo a cada execução de pipeline, por isso, defina a data de fim adequadamente. 
1. Na página **Parâmetros da Execução do Acionador**, reveja o aviso e, em seguida, selecione **Concluir**. O pipeline neste exemplo não tem nenhum parâmetro. 

1. Clique em **Publicar tudo** para publicar a alteração. 

1. Vá para o separador **Monitorizar**, do lado esquerdo, para ver as execuções do pipeline acionadas. 

    ![Execuções de pipeline acionadas](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Para alternar da exibição de execuções de **pipeline** para o modo de exibição de execuções de **gatilho** , selecione **gatilho é executado** na parte superior da janela.

1. Verá as execuções do acionador numa lista. 

1. Confirme que estão inseridas duas linhas por minuto (em cada execução do pipeline) na tabela **emp** até à hora de fim especificada. 

## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização no Armazenamento de blobs. Aprendeu a: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um pipeline com uma atividade de cópia.
> * Testar a execução do pipeline.
> * Acionar o pipeline manualmente.
> * Acionar o pipeline com base numa agenda.
> * Monitorizar o pipeline e execuções de atividades.


Avance para o tutorial seguinte para saber como copiar dados do plano local para a cloud: 

> [!div class="nextstepaction"]
>[Copiar dados do plano local para a cloud](tutorial-hybrid-copy-portal.md)
