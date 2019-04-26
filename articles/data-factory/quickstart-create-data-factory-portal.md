---
title: Criar uma fábrica de dados do Azure com a IU do Azure Data Factory | Microsoft Docs
description: Crie uma fábrica de dados com um pipeline que copia dados de uma localização no armazenamento de Blobs do Azure para outra localização.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 6f5a4e04c0d135e85624b04dbcdcda6b7d15a427
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315562"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Início rápido: Criar uma fábrica de dados com a IU do Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-portal.md)

Este início rápido descreve como utilizar a UI do Azure Data Factory para criar e monitorizar uma fábrica de dados. O pipeline que criar nesta fábrica de dados *copia* dados de uma pasta para outra pasta num armazenamento de Blobs do Azure. Para obter um tutorial sobre como *transformar* dados com o Azure Data Factory, consulte [Tutorial: Transformar dados com o Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Se não estiver familiarizado com o Azure Data Factory, veja [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de começar o início rápido. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo ajuda-o a compreender a IU do Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Aceda ao [Portal do Azure](https://portal.azure.com). 
1. Selecione **Criar um recurso** no menu esquerdo, selecione **Análise** e, em seguida, selecione **Data Factory**. 
   
   ![Seleção do Data Factory no painel "Novo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
1. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**. 
      
   ![Página "Nova fábrica de dados"](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, **&lt;oseunome&gt;ADFTutorialDataFactory**) e tente criá-la novamente. Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
1. Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados. 
1. Em **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
   Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
1. Em **Versão**, selecione **V2**.
1. Em **Localização**, selecione a localização para a fábrica de dados.

   A lista mostra apenas as localizações suportadas pelo Data Factory e onde serão armazenados os seus metadados do Azure Data Factory. Tenha em atenção que os arquivos de dados associados (como o Armazenamento do Microsoft Azure e a Base de Dados SQL do Azure) e as computações (como o Azure HDInsight) utilizados pelo Data Factory podem ser executados noutras regiões.

1. Selecione **Criar**.

1. Após concluir a criação, verá a página **Data Factory**. Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte.
   
   ![Home page da fábrica de dados, com o mosaico "Criar e Monitorizar"](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
1. Na página **Introdução**, mude para o separador **Criar**, no painel esquerdo. 

    ![Página “Vamos começar”](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Criar um serviço ligado
Neste procedimento, vai criar um serviço ligado para ligar a sua conta de armazenamento do Azure à fábrica de dados. O serviço ligado tem as informações de ligação utilizadas pelo serviço Data Factory em runtime para se ligar ao mesmo.

1. Selecione **Ligações** e, em seguida, selecione o botão **Novo** na barra de ferramentas. 

   ![Botões para criar uma nova ligação](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
1. Na página **Novo Serviço Ligado**, selecione **Armazenamento de Blobs do Azure** e selecione **Continuar**. 

   ![Selecionar o mosaico “Armazenamento de Blobs do Azure”](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. Conclua os seguintes passos: 

   a. Em **Nome**, introduza **AzureStorageLinkedService**.

   b. Em **Nome da conta de armazenamento**, selecione o nome da sua conta de armazenamento do Azure.

   c. Selecione **Testar ligação** para confirmar que o serviço Data Factory consegue ligar à conta de armazenamento. 

   d. Para guardar o serviço ligado, selecione **Terminar**. 

   ![Definições do serviço ligado do Armazenamento do Azure](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste procedimento, vai criar dois conjuntos de dados: **InputDataset** e **OutputDataset**. Estes conjuntos de dados são do tipo **AzureBlob**. Dizem respeito ao serviço ligado do Armazenamento do Azure que criou na secção anterior. 

O conjunto de dados de entrada representa a origem de dados na pasta de entrada. Na definição do conjunto de dados de entrada, vai especificar o contentor de blobs (**adftutorial**), a pasta (**input**) e o ficheiro (**emp.txt**) que contêm os dados de origem. 

O conjunto de dados de saída representa os dados que são copiados para o destino. Na definição do conjunto de dados de saída, vai especificar o contentor de blobs (**adftutorial**), a pasta (**output**) e o ficheiro para o qual os dados vão ser copiados. Cada execução de um pipeline tem um ID exclusivo associado. Pode aceder a este ID com a variável do sistema **RunId**. O nome do ficheiro de saída é avaliado dinamicamente com base no ID da execução do pipeline.   

Nas definições do serviço ligado, vai especificar a conta de Armazenamento do Azure que contém os dados de origem. Nas definições do conjunto de dados de origem, vai especificar onde é que os dados de origem residem exatamente (contentor de blobs, pasta e ficheiro). Nas definições do conjunto de dados de sink, vai especificar para onde é que os dados vão ser copiados (contentor de blobs, pasta e ficheiro). 
 
1. Selecione o botão **+** (mais) e, em seguida, selecione **Conjunto de Dados**.

   ![Menu para criar um conjunto de dados](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**. 

   ![Selecionar “Armazenamento de Blobs do Azure”](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. No separador **Geral** do conjunto de dados, introduza **InputDataset** em **Nome**. 

1. Mude para o separador **Ligação** e conclua os seguintes passos: 

    a. Em **Serviço ligado**, selecione **AzureStorageLinkedService**.

    b. Em **Caminho do ficheiro**, selecione o botão **Procurar**.

    ![Separador “Ligação” e botão “Procurar”](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. Na janela **Escolher um ficheiro ou pasta**, navegue para a pasta **input** do contentor **adftutorial**, selecione **emp.txt** e selecione **Concluir**.

    ![Procurar o ficheiro de entrada](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
    d. (opcional) Selecione **Pré-visualizar dados** para pré-visualizar os dados no ficheiro emp.txt.     

1. Repita os passos para criar o conjunto de dados de saída:  

   a. Selecione o botão **+** (mais) e, em seguida, selecione **Conjunto de Dados**.

   b. Na página **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e, em seguida, selecione **Concluir**.

   c. Na tabela **Geral**, especifique **OutputDataset** no nome.

   d. No separador **Ligação**, selecione **AzureStorageLinkedService** como serviço ligado e introduza **adftutorial/output** para a pasta, no campo de diretório. Se a pasta **saída** não existir, a atividade de cópia cria a mesma no runtime.

## <a name="create-a-pipeline"></a>Criar um pipeline 
Neste procedimento, vai criar e validar um pipeline com uma atividade de cópia que utiliza os conjuntos de dados de entrada e saída. A atividade de cópia copia dados do ficheiro especificado nas definições do conjunto de dados de entrada para o ficheiro especificado nas definições do conjunto de dados de saída. Se o conjunto de dados de entrada especifica apenas uma pasta (e não o nome do ficheiro), a atividade de cópia copia todos os ficheiros na pasta de origem para o destino. 

1. Selecione o botão **+** (mais) e, em seguida, selecione **Pipeline**. 

   ![Menu para criar um novo pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
1. No separador **Geral**, especifique **CopyPipeline** no **Nome**. 

1. Na caixa de ferramentas **Atividades**, expanda **Mover e Transformar**. Arraste a atividade **Copiar** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline. Também pode pesquisar por atividades na caixa de ferramentas **Atividades**. Especifique **CopyFromBlobToBlob** em **Nome**.

   ![Definições gerais da atividade Copy](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
1. Mude para o separador **Origem** nas definições da atividade de cópia e selecione **InputDataset** em **Conjunto de Dados de Origem**.

1. Mude para o separador **Sink** nas definições da atividade de cópia e selecione **OutputDataset** em **Conjunto de Dados de Sink**.

1. Clique em **Validar** na barra de ferramentas do pipeline acima da tela, para validar as definições do pipeline. Confirme que o pipeline foi confirmado com êxito. Para fechar a saída da validação, selecione o botão **>>** (seta para a direita). 

## <a name="debug-the-pipeline"></a>Depurar o pipeline
Neste passo, vai depurar o pipeline antes de o implementar no Data Factory. 

1. Na barra de ferramentas do pipeline acima da tela, clique em **Depurar** para acionar uma execução de testes. 
    
1. Confirme se vê o estado da execução do pipeline no separador **Saída** das definições do pipeline na parte inferior. 

1. Confirme que vê um ficheiro de saída na pasta **output** do contentor **adftutorial**. Se a pasta de saída não existir, o serviço Data Factory cria-a automaticamente. 

## <a name="trigger-the-pipeline-manually"></a>Acionar o pipeline manualmente
Neste procedimento, vai implementar entidades (serviços ligados, conjuntos de dados, pipelines) no Azure Data Factory. Em seguida, vai acionar manualmente uma execução de pipeline. 

1. Antes de acionar um pipeline, tem de publicar entidades no Data Factory. Para publicar, selecione **Publicar Tudo**, na parte superior. 

   ![Botão Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
1. Para acionar o pipeline manualmente, selecione **Acionar**, na barra de ferramentas do mesmo e, em seguida, selecione **Acionar Agora**. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Utilize o botão **Atualizar** para atualizar a lista.

   ![Separador para monitorizar as execuções do pipeline, com o botão "Atualizar"](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Selecione a ligação **Ver Execuções de Atividades**, em **Ações**. Vai ver o estado da execução da atividade de cópia nesta página. 

   ![Execuções de atividades do pipeline](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
1. Para ver os detalhes da operação de cópia, selecione a ligação **Detalhes** (imagem de óculos), na coluna **Ações**. Para obter os detalhes das propriedades, veja [Copy Activity overview](copy-activity-overview.md) (Descrição geral da Atividade Copy). 

   ![Detalhes da operação de cópia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Confirme que vê um ficheiro novo na pasta **output**. 
1. Pode regressar à vista **Execuções do Pipeline** a partir da vista **Execuções de Atividades**, selecionando a ligação **Pipelines**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Acionar o pipeline com base numa agenda
Este procedimento é opcional neste tutorial. Pode criar um *acionador de agenda* para agendar a execução do pipeline periodicamente (hora a hora, diariamente, etc.). Neste procedimento, vai criar um acionador para ser executado a cada minuto até à data e hora de fim que especificar. 

1. Mude para o separador **Criar**. 

1. Aceda ao seu pipeline, selecione **Acionador** na barra de ferramentas do pipeline e, em seguida, selecione **Novo/Editar**. 

1. Na página **Adicionar Acionadores**, selecione **Escolher acionador**e, em seguida, selecione **Novo**. 

1. Na página **Novo Acionador**, em **Fim**, selecione **Na Data**, especifique a hora de fim para alguns minutos depois da hora atual e selecione **Aplicar**. 

   Existe um custo associado a cada execução de pipeline, pelo que deve especificar a hora de fim apenas para alguns minutos após a hora de início. Confirme que estão marcadas para o mesmo dia. No entanto, certifique-se de que há tempo suficiente para o pipeline ser executado entre a hora de publicação e a hora de fim. O acionador só entra em vigor depois de publicar a solução no Data Factory e não quando guarda o acionador na IU. 

   ![Definições do acionador](./media/quickstart-create-data-factory-portal/trigger-settings.png)
1. Na página **Novo Acionador**, selecione a caixa de verificação **Ativado** e selecione **Seguinte**. 

   ![Caixa de verificação “Ativado” e botão “Seguinte”](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Reveja a mensagem de aviso e selecione **Concluir**.

   ![Botão de aviso e “Concluir”](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
1. Selecione **Publicar Tudo** para publicar as alterações no Data Factory. 

1. Mude para o separador **Monitorizar**, no lado esquerdo. Selecione **Atualizar** para atualizar a lista. Pode ver que o pipeline é executado uma vez a cada minuto desde a hora de publicação até à hora de fim. 

   Repare nos valores na coluna **Acionado Por**. A execução do acionador manual provém do passo (**Acionar Agora**) que realizou anteriormente. 

   ![Lista de execuções acionadas](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
1. Mude para a vista de **Execuções do Acionador**. 

   ![Mudar para a vista de “Execuções do Acionador”](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
1. Confirme que é criado um ficheiro de saída para todas as execuções de pipelines até à data e hora de fim especificada na pasta de **saída**. 

## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de Blobs do Azure. Para saber como utilizar o Data Factory em mais cenários, aceda aos [tutoriais](tutorial-copy-data-portal.md). 