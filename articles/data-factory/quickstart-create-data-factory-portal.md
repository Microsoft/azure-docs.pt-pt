---
title: Criar uma fábrica de dados Azure utilizando a UI da Azure Data Factory
description: Crie uma fábrica de dados com um pipeline que copia dados de uma localização no armazenamento de Blobs do Azure para outra localização.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655105"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Início Rápido: criar uma fábrica de dados através da interface de utilizador do Azure Data Factory 

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este início rápido descreve como utilizar a UI do Azure Data Factory para criar e monitorizar uma fábrica de dados. O pipeline que criar nesta fábrica de dados *copia* dados de uma pasta para outra pasta num armazenamento de Blobs do Azure. Para *transformar* os dados utilizando a Azure Data Factory, consulte [o fluxo de dados de mapeamento](concepts-data-flow-overview.md).

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md) antes de começar o início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo ajuda-o a compreender a IU do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Aceda ao [Portal do Azure](https://portal.azure.com). 
1. No menu do portal do Azure, selecione **Criar um recurso**.
1. Selecione **Integração** e, em seguida, selecione **Data Factory**. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Seleção de Fábrica de Dados no Novo Painel.":::

1. Na página **'Criar Fábrica de** Dados', no separador **Basics,** selecione a **subscrição** Azure na qual pretende criar a fábrica de dados.
1. Em **Grupo de Recursos**, efetue um destes passos:

    a. Selecione um grupo de recursos existente da lista de drop-down.

    b. **Selecione Criar novo** e insira o nome de um novo grupo de recursos.
    
    Para saber mais sobre grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md). 

1. Para **a Região**, selecione a localização para a fábrica de dados.

   A lista mostra apenas as localizações suportadas pelo Data Factory e onde serão armazenados os seus metadados do Azure Data Factory. As lojas de dados associadas (como a Azure Storage e a Azure SQL Database) e os cálculos (como o Azure HDInsight) que a Data Factory utiliza podem funcionar noutras regiões.
 
1. Para **citar ,** insira a **ADFTutorialDataFactory**.
   O nome da fábrica de dados Azure deve ser *globalmente único.* Se vir o seguinte erro, altere o nome da fábrica de dados (por exemplo, **&lt; o seu nome &gt; ADFTutorialDataFactory)** e tente criar novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nova mensagem de erro da fábrica de dados para nome duplicado.":::

1. Em **Versão**, selecione **V2**.

1. Selecione **Seguinte: Configuração git** e, em seguida, selecione **Configure Git mais tarde** caixa de verificação.

1. Selecione **Rever + criar** e selecione **Criar** depois de passar a validação. Depois de concluída a criação, selecione **Ir para o recurso** para navegar para a página data **Factory.** 

1. Selecione o **azulejo do Monitor & autor** para iniciar a aplicação de interface de utilizador (UI) da Azure Data Factory num separado separado do navegador.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Página inicial da Fábrica de Dados Azure, com o azulejo do Monitor de autores &.":::
   
   > [!NOTE]
   > Se vir que o navegador está preso em "Autorizar", limpe os **cookies de terceiros do Bloco e** a caixa de verificação de dados do site. Ou mantenha-a selecionada, crie uma exceção para **login.microsoftonline.com**, e depois tente abrir novamente a app.
   

## <a name="create-a-linked-service"></a>Criar um serviço ligado
Neste procedimento, cria um serviço ligado para ligar a sua conta de Armazenamento Azure à fábrica de dados. O serviço ligado tem as informações de ligação utilizadas pelo serviço Data Factory em runtime para se ligar ao mesmo.

1. Na página UI da Azure Data Factory, abra o [**separador Gerir**](./author-management-hub.md) a partir do painel esquerdo.

1. Na página de serviços Linked, selecione **+Novo** para criar um novo serviço ligado.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Novo serviço ligado.":::
   
1. Na página **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

1. Na página New Linked Service (Azure Blob Storage), complete os seguintes passos: 

   a. Em **Nome**, introduza **AzureStorageLinkedService**.

   b. Para **o nome da conta de armazenamento,** selecione o nome da sua conta Azure Storage.

   c. Selecione **Testar ligação** para confirmar que o serviço Data Factory consegue ligar à conta de armazenamento. 

   d. Selecione **Criar** para salvar o serviço ligado. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Serviço ligado.":::


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste procedimento, vai criar dois conjuntos de dados, **InputDataset** e **OutputDataset**. Estes conjuntos de dados são do tipo **AzureBlob**. Dizem respeito ao serviço ligado do Armazenamento do Azure que criou na secção anterior. 

O conjunto de dados de entrada representa a origem de dados na pasta de entrada. Na definição do conjunto de dados de entrada, vai especificar o contentor de blobs (**adftutorial**), a pasta (**input**) e o ficheiro (**emp.txt**) que contêm os dados de origem. 

O conjunto de dados de saída representa os dados que são copiados para o destino. Na definição do conjunto de dados de saída, vai especificar o contentor de blobs (**adftutorial**), a pasta (**output**) e o ficheiro para o qual os dados vão ser copiados. Cada execução de um pipeline tem um ID exclusivo associado. Pode aceder a este ID com a variável do sistema **RunId**. O nome do ficheiro de saída é avaliado dinamicamente com base no ID da execução do pipeline.   

Nas definições de serviço ligadas, especificou a conta de Armazenamento Azure que contém os dados de origem. Nas definições do conjunto de dados de origem, vai especificar onde é que os dados de origem residem exatamente (contentor de blobs, pasta e ficheiro). Nas definições do conjunto de dados de sink, vai especificar para onde é que os dados vão ser copiados (contentor de blobs, pasta e ficheiro). 

1. **Selecione O** separador autor do painel esquerdo.

1. Selecione o **+** botão (mais) e, em seguida, **selecione Dataset**.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Menu para criar um conjunto de dados.":::

1. Na página **New Dataset,** selecione **Azure Blob Storage** e, em seguida, selecione **Continue**. 

1. Na página **'Selecionar Formato',** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continue**. Neste caso, selecione **Binary** quando copiar ficheiros como-está sem analisar o conteúdo.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Selecione o formato.":::   
   
1. Na página **'Definir Propriedades',** complete os seguintes passos:

    a. Em **Nome**, insira **o InputDataset**. 

    b. Em **Serviço ligado**, selecione **AzureStorageLinkedService**.

    c. Em **Caminho do ficheiro**, selecione o botão **Procurar**.

    d. Na janela **de ficheiro ou pasta Escolha,** navegue na pasta de **entrada** no recipiente **adftutorial,** selecione o ficheiro **emp.txt** e, em seguida, selecione **OK**.
    
    e. Selecione **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Definir propriedades para InputDataset.":::

1. Repita os passos para criar o conjunto de dados de saída:  

    a. Selecione o **+** botão (mais) e, em seguida, **selecione Dataset**.

    b. Na página **New Dataset,** selecione **Azure Blob Storage** e, em seguida, selecione **Continue**.

    c. Na página **'Selecionar Formato',** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continue**.

    d. Na página **'Definir propriedades',** especifique **o Conjunto de Dados de Saída** para o nome. Selecione **AzureStorageLinkedService** como serviço ligado.

    e. No **caminho do ficheiro,** **introduza adftutorial/saída**. Se a pasta **de saída** não existir, a atividade da cópia cria-a em tempo de execução.

    f. Selecione **OK**.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="Definir propriedades para OutputDataset.":::    

## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste procedimento, vai criar e validar um pipeline com uma atividade de cópia que utiliza os conjuntos de dados de entrada e saída. A atividade de cópia copia dados do ficheiro especificado nas definições do conjunto de dados de entrada para o ficheiro especificado nas definições do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (e não o nome do ficheiro), a atividade de cópia copia todos os ficheiros na pasta de origem para o destino. 

1. Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline**. 

1. No painel geral de **propriedades**, especifique **copyPipeline** para **nome**. Em seguida, desabar o painel clicando no ícone Propriedades no canto superior direito.

1. Na caixa de ferramentas **Atividades**, expanda **Mover e Transformar**. Arraste a atividade **de Copiar dados** da caixa de **ferramentas De atividades** para a superfície do designer de gasodutos. Também pode pesquisar por atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** em **Nome**.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Criar uma atividade de dados de cópia.":::   

1. Mude para o separador **Origem** nas definições da atividade de cópia e selecione **InputDataset** em **Conjunto de Dados de Origem**.

1. Mude para o separador **Sink** nas definições da atividade de cópia e selecione **OutputDataset** em **Conjunto de Dados de Sink**.

1. Clique em **Validar** na barra de ferramentas do pipeline acima da tela, para validar as definições do pipeline. Confirme que o pipeline foi confirmado com êxito. Para fechar a saída de validação, selecione o botão de validação no canto superior direito. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Valide um oleoduto.":::

## <a name="debug-the-pipeline"></a>Depurar o pipeline
Neste passo, vai depurar o pipeline antes de o implementar no Data Factory. 

1. Na barra de ferramentas do pipeline acima da tela, clique em **Depurar** para acionar uma execução de testes. 
    
1. Confirme se vê o estado da execução do pipeline no separador **Saída** das definições do pipeline na parte inferior. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Saída de execução do gasoduto":::

1. Confirme que vê um ficheiro de saída na pasta **output** do contentor **adftutorial**. Se a pasta de saída não existir, o serviço Data Factory cria-a automaticamente. 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente
Neste procedimento, vai implementar entidades (serviços ligados, conjuntos de dados, pipelines) no Azure Data Factory. Em seguida, vai acionar manualmente uma execução de pipeline. 

1. Antes de acionar um pipeline, tem de publicar entidades no Data Factory. Para publicar, **selecione Publicar tudo** no topo. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Publique tudo.":::    

1. Para ativar o gasoduto manualmente, **selecione Adicionar Gatilho** na barra de ferramentas do gasoduto e, em seguida, selecione Trigger **Now**. Na página **de execução pipeline,** selecione **OK**.

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Utilize o botão **Atualizar** para atualizar a lista.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="Separador para monitorização do gasoduto funciona"::: 
1. Selecione o link **CopyPipeline,** verá o estado da atividade da cópia executado nesta página. 

1. Para ver detalhes sobre a operação da cópia, selecione o link **Detalhes** (imagem de óculos). Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Copiar detalhes da operação.":::
1. Confirme que vê um ficheiro novo na pasta **output**. 
1. Pode voltar a mudar para a vista de **execuções** do Pipeline a partir da vista **"Atividade"** selecionando a ligação **"Todas as corre" do gasoduto.** 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Acionar o pipeline com base numa agenda
Este procedimento é opcional neste tutorial. Pode criar um *acionador de agenda* para agendar a execução do pipeline periodicamente (hora a hora, diariamente, etc.). Neste procedimento, vai criar um acionador para ser executado a cada minuto até à data e hora de fim que especificar. 

1. Mude para o separador **Criar**. 

1. Vá ao seu oleoduto, **selecione Adicionar Gatilho** na barra de ferramentas do gasoduto e, em seguida, selecione **Novo/Editar**. 

1. Na página **Adicionar Acionadores**, selecione **Escolher acionador** e, em seguida, selecione **Novo**. 

1. Na página **Novo Gatilho,** em **'Fim'**, selecione **On Date**, especifique um tempo final alguns minutos após a hora atual e, em seguida, selecione **OK**. 

   Existe um custo associado a cada execução de pipeline, pelo que deve especificar a hora de fim apenas para alguns minutos após a hora de início. Confirme que estão marcadas para o mesmo dia. No entanto, certifique-se de que há tempo suficiente para o oleoduto funcionar entre o tempo de publicação e o fim do tempo. O acionador só entra em vigor depois de publicar a solução no Data Factory e não quando guarda o acionador na IU. 

1. Na página **Novo Gatilho,** selecione a caixa **de verificação ativada** e, em seguida, selecione **OK**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Nova definição do gatilho.":::   
1. Reveja a mensagem de aviso e selecione **OK**.

1. **Selecione Publicar tudo** para publicar alterações na Data Factory. 

1. Mude para o separador **Monitorizar**, no lado esquerdo. Selecione **Atualizar** para atualizar a lista. Pode ver que o pipeline é executado uma vez a cada minuto desde a hora de publicação até à hora de fim. 

   Observe os valores na coluna **TRIGGERED BY.** A execução do acionador manual provém do passo (**Acionar Agora**) que realizou anteriormente. 

1. O switch para a vista **de execução** do gatilho. 

1. Confirme que é criado um ficheiro de saída para todas as execuções de pipelines até à data e hora de fim especificada na pasta de **saída**. 

## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. Para saber como utilizar o Data Factory em mais cenários, aceda aos [tutoriais](tutorial-copy-data-portal.md).
