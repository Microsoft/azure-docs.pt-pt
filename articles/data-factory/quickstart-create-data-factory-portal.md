---
title: Criar uma fábrica de dados Azure utilizando a Azure Data Factory UI
description: Crie uma fábrica de dados com um pipeline que copia dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: c54d3187af1faee3a47a794dcf5b9b91733e5592
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683350"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Quickstart: Criar uma fábrica de dados utilizando a UI da Fábrica de Dados Azure

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este início rápido descreve como utilizar a UI do Azure Data Factory para criar e monitorizar uma fábrica de dados. O pipeline que criar nesta fábrica de dados *copia* dados de uma pasta para outra pasta num armazenamento de Blobs do Azure. Para *transformar* dados utilizando a Azure Data Factory, consulte o fluxo de [dados de mapeamento](concepts-data-flow-overview.md) e o fluxo de [dados de wrangling (Pré-visualização)](wrangling-data-flow-overview.md).

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](introduction.md) antes de começar o início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo ajuda-o a compreender a IU do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Vá ao [portal Azure.](https://portal.azure.com) 
1. A partir do menu do portal Azure, selecione **Criar um recurso**.
   
   ![Selecione Criar um recurso do menu do portal Azure](./media/doc-common-process/create-a-resource.png)
1. Selecione **Analytics**, e, em seguida, selecione **Data Factory**. 
   
   ![Seleção do Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**. 
 
   O nome da fábrica de dados Azure deve ser *globalmente único.* Se vir o seguinte erro, altere o nome da fábrica de dados (por exemplo, ** &lt; o seu nome &gt; ADFTutorialDataFactory**) e tente criar novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/doc-common-process/name-not-available-error.png)
1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Em **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados.

   A lista mostra apenas as localizações suportadas pelo Data Factory e onde serão armazenados os seus metadados do Azure Data Factory. As lojas de dados associadas (como o Azure Storage e o Azure SQL Database) e os cálculos (como o Azure HDInsight) que a Data Factory utiliza podem ser executados noutras regiões.

1. Selecione **Criar**. Depois de concluída a criação, selecione **Recorrer para** navegar na página **data Factory.** 

1. Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte.
   
   ![Home page da fábrica de dados, com o mosaico "Criar e Monitorizar"](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Se verificar que o navegador está preso em "Autorizar", limpe os **cookies de terceiros** do Bloco e a caixa de verificação de dados do site. Ou mantê-la selecionada, criar uma exceção para **login.microsoftonline.com**, e depois tentar abrir a app novamente.
   
1. Na página **Introdução**, mude para o separador **Criar**, no painel esquerdo. 

    ![Página “Vamos começar”](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Criar um serviço ligado
Neste procedimento, cria um serviço ligado para ligar a sua conta de Armazenamento Azure à fábrica de dados. O serviço ligado tem as informações de ligação utilizadas pelo serviço Data Factory em runtime para se ligar ao mesmo.

1. Selecione **Ligações**e, em seguida, selecione **o** novo botão na barra de ferramentas (o botão**de ligações** está localizado na parte inferior da coluna esquerda sob **recursos de fábrica).** 

1. Na página **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

1. Na página New Linked Service (Azure Blob Storage), complete os seguintes passos: 

   a. Em **Nome**, introduza **AzureStorageLinkedService**.

   b. Para o nome da **conta de armazenamento,** selecione o nome da sua conta De armazenamento Azure.

   c. Selecione **Testar ligação** para confirmar que o serviço Data Factory consegue ligar à conta de armazenamento. 

   d. Selecione **Criar** para salvar o serviço ligado. 

      ![Novo serviço ligado](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste procedimento, vai criar dois conjuntos de dados, **InputDataset** e **OutputDataset**. Estes conjuntos de dados são do tipo **AzureBlob**. Dizem respeito ao serviço ligado do Armazenamento do Azure que criou na secção anterior. 

O conjunto de dados de entrada representa a origem de dados na pasta de entrada. Na definição do conjunto de dados de entrada, vai especificar o contentor de blobs (**adftutorial**), a pasta (**input**) e o ficheiro (**emp.txt**) que contêm os dados de origem. 

O conjunto de dados de saída representa os dados que são copiados para o destino. Na definição do conjunto de dados de saída, vai especificar o contentor de blobs (**adftutorial**), a pasta (**output**) e o ficheiro para o qual os dados vão ser copiados. Cada execução de um pipeline tem um ID exclusivo associado. Pode aceder a este ID com a variável do sistema **RunId**. O nome do ficheiro de saída é avaliado dinamicamente com base no ID da execução do pipeline.   

Nas definições de serviço ligadas, especificou a conta De armazenamento azure que contém os dados de origem. Nas definições do conjunto de dados de origem, vai especificar onde é que os dados de origem residem exatamente (contentor de blobs, pasta e ficheiro). Nas definições do conjunto de dados de sink, vai especificar para onde é que os dados vão ser copiados (contentor de blobs, pasta e ficheiro). 
 
1. Selecione o **+** botão (mais) e, em seguida, selecione **Dataset**.

   ![Menu para criar um conjunto de dados](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na página **New Dataset,** selecione **Armazenamento De Blob Azure**, e, em seguida, selecione **Continuar**. 

1. Na página **Select Format,** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**. Neste caso, **selecione Binary** quando copiar ficheiros como está sem analisar o conteúdo.

   ![Selecione formato](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. Na página **Definição de Propriedades,** complete os seguintes passos:

    a. Em **nome,** introduza **O Conjunto de Dados de Entrada**. 

    b. Em **Serviço ligado**, selecione **AzureStorageLinkedService**.

    c. Em **Caminho do ficheiro**, selecione o botão **Procurar**.

    d. Na **janela Escolha um ficheiro ou uma janela** de pasta, navegue na pasta de **entrada** no recipiente **adftutorial, selecione** o ficheiro **emp.txt** e, em seguida, selecione **OK**.
    
    e. Selecione **OK**.   

    ![Definir propriedades para InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Repita os passos para criar o conjunto de dados de saída:  

    a. Selecione o **+** botão (mais) e, em seguida, selecione **Dataset**.

    b. Na página **New Dataset,** selecione **Armazenamento De Blob Azure**, e, em seguida, selecione **Continuar**.

    c. Na página **Select Format,** escolha o tipo de formato dos seus dados e, em seguida, selecione **Continuar**.

    d. Na página **Definição** de Propriedades, especifique **o OutputDataset** para o nome. Selecione **AzureStorageLinkedService** como serviço ligado.

    e. Sob **o caminho do Ficheiro,** introduza **adftutorial/output**. Se a pasta de **saída** não existir, a atividade da cópia cria-a no prazo de execução.

    f. Selecione **OK**.   

    ![Definir propriedades para OutputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste procedimento, vai criar e validar um pipeline com uma atividade de cópia que utiliza os conjuntos de dados de entrada e saída. A atividade de cópia copia dados do ficheiro especificado nas definições do conjunto de dados de entrada para o ficheiro especificado nas definições do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (e não o nome do ficheiro), a atividade de cópia copia todos os ficheiros na pasta de origem para o destino. 

1. Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline**. 

1. No separador **Geral**, especifique **CopyPipeline** no **Nome**. 

1. Na caixa de ferramentas **Atividades**, expanda **Mover e Transformar**. Arraste a atividade de Dados de **Cópia** da caixa de **ferramentas De Atividades** para a superfície do designer de gasodutos. Também pode pesquisar por atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** em **Nome**.
   ![Criação de uma atividade de dados de cópia](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Mude para o separador **Origem** nas definições da atividade de cópia e selecione **InputDataset** em **Conjunto de Dados de Origem**.

1. Mude para o separador **Sink** nas definições da atividade de cópia e selecione **OutputDataset** em **Conjunto de Dados de Sink**.

1. Clique em **Validar** na barra de ferramentas do pipeline acima da tela, para validar as definições do pipeline. Confirme que o pipeline foi confirmado com êxito. Para fechar a saída de validação, selecione o **>>** botão (seta direita). 
   ![Validar um oleoduto](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Depurar o pipeline
Neste passo, vai depurar o pipeline antes de o implementar no Data Factory. 

1. Na barra de ferramentas do pipeline acima da tela, clique em **Depurar** para acionar uma execução de testes. 
    
1. Confirme se vê o estado da execução do pipeline no separador **Saída** das definições do pipeline na parte inferior. 
 
    ![Saída de execução de gasoduto](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Confirme que vê um ficheiro de saída na pasta **output** do contentor **adftutorial**. Se a pasta de saída não existir, o serviço Data Factory cria-a automaticamente. 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente
Neste procedimento, vai implementar entidades (serviços ligados, conjuntos de dados, pipelines) no Azure Data Factory. Em seguida, vai acionar manualmente uma execução de pipeline. 

1. Antes de acionar um pipeline, tem de publicar entidades no Data Factory. Para publicar, selecione **Publicar tudo** no topo. 
    ![Publicar tudo](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Para acionar manualmente o gasoduto, selecione **Adicionar gatilho** na barra de ferramentas do gasoduto e, em seguida, selecione **Trigger Now**. Na página **de execução** do Pipeline, selecione **Finish**.

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Utilize o botão **Atualizar** para atualizar a lista.

   ![Separador para monitorização do gasoduto funciona](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Selecione o link **CopyPipeline** e verá o estado da atividade da cópia ser executado nesta página. 

1. Para ver detalhes sobre o funcionamento da cópia, selecione o link **Detalhes** (imagem de óculos) . Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy). 

   ![Detalhes da operação de cópia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Confirme que vê um ficheiro novo na pasta **output**. 
1. Pode voltar a ser a vista de **pipeline** a partir da vista **de funcionamento** da Atividade selecionando a ligação de funcionamento do **gasoduto All.** 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Acionar o pipeline com base numa agenda
Este procedimento é opcional neste tutorial. Pode criar um *acionador de agenda* para agendar a execução do pipeline periodicamente (hora a hora, diariamente, etc.). Neste procedimento, vai criar um acionador para ser executado a cada minuto até à data e hora de fim que especificar. 

1. Mude para o separador **Criar**. 

1. Vá ao seu pipeline, selecione **Adicionar Gatilho** na barra de ferramentas do gasoduto e, em seguida, selecione **New/Edit**. 

1. Na página **Adicionar Acionadores**, selecione **Escolher acionador**e, em seguida, selecione **Novo**. 

1. Na página **New Trigger,** em **'Fim ',** selecione **On Date**',' especifique um tempo de fim alguns minutos após a hora atual e, em seguida, selecione **OK**. 

   Existe um custo associado a cada execução de pipeline, pelo que deve especificar a hora de fim apenas para alguns minutos após a hora de início. Confirme que estão marcadas para o mesmo dia. No entanto, certifique-se de que há tempo suficiente para que o oleoduto decorra entre o tempo de publicação e o tempo final. O acionador só entra em vigor depois de publicar a solução no Data Factory e não quando guarda o acionador na IU. 

1. Na página **New Trigger,** selecione a caixa de verificação **Ativada** e, em seguida, selecione **OK**. 

   ![Nova definição do gatilho](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Reveja a mensagem de aviso e selecione **OK**.

1. **Selecione Publicar tudo** para publicar alterações na Data Factory. 

1. Mude para o separador **Monitorizar**, no lado esquerdo. Selecione **Atualizar** para atualizar a lista. Pode ver que o pipeline é executado uma vez a cada minuto desde a hora de publicação até à hora de fim. 

   Note os valores na coluna **TRIGGERED BY.** A execução do acionador manual provém do passo (**Acionar Agora**) que realizou anteriormente. 

1. Mude para a vista **de funcionação** do Gatilho. 

1. Confirme que é criado um ficheiro de saída para todas as execuções de pipelines até à data e hora de fim especificada na pasta de **saída**. 

## <a name="next-steps"></a>Próximos passos
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. Para saber como utilizar o Data Factory em mais cenários, aceda aos [tutoriais](tutorial-copy-data-portal.md). 
