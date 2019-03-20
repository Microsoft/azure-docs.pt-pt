---
title: Executar um Databricks Notebook com a atividade Databricks Notebook no Azure Data Factory
description: Saiba como pode utilizar a Atividade do Databricks Notebook num Azure Data Factory para executar um Databricks Notebook no cluster de tarefas do Databricks.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: fcf3fe85b6dd184b4f58060aab4fcf3ce146af13
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58015168"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Executar um Databricks Notebook com a Atividade do Databricks Notebook no Azure Data Factory

Neste tutorial, utiliza o portal do Azure para criar um pipeline do Azure Data Factory que executa um Databricks Notebook no cluster de tarefas do Databricks. Também transmite os parâmetros do Azure Data Factory ao Databricks Notebook durante a execução.

Vai executar os seguintes passos neste tutorial:

  - Criar uma fábrica de dados.

  - Crie um pipeline que utilize a atividade do Databricks Notebook.

  - Acionar uma execução de pipeline.

  - Monitorizar a execução do pipeline.

Se não tiver uma subscrição do Azure, crie uma  [conta gratuita](https://azure.microsoft.com/free/)  antes de começar.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Pré-requisitos

  - **Área de trabalho do Azure Databricks**. [Crie uma área de trabalho do Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ou utilize uma já existente. Crie um Python Notebook na sua área de trabalho do Azure Databricks. Em seguida, execute o Notebook e transmita os parâmetros ao mesmo através do Azure Data Factory.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1.  Inicie **Microsoft Edge** ou **Google Chrome** navegador da web. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.

1.  Selecione **criar um recurso** no menu da esquerda, selecione **análise**e, em seguida, selecione **Data Factory**.

    ![Criar uma nova fábrica de dados](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Na **nova fábrica de dados** painel, introduza **ADFTutorialDataFactory** sob **nome**.

    O nome da fábrica de dados do Azure tem de ser *globalmente exclusivo*. Se vir o seguinte erro, altere o nome da fábrica de dados. (Por exemplo, usar **\<yourname\>ADFTutorialDataFactory**). Para regras de nomenclatura dos artefactos do Data Factory, consulte a [Data Factory – regras de nomenclatura](https://docs.microsoft.com/azure/data-factory/naming-rules) artigo.

    ![Indicar um nome para a nova fábrica de dados](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Para **subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados.

1.  Para **grupo de recursos**, realize um dos seguintes passos:
    
    - Selecione **utilizar existente** e selecione um grupo de recursos existente na lista pendente.
    
    - Selecione **criar novo** e introduza o nome de um grupo de recursos.

    Alguns dos passos neste início rápido pressupõem que utiliza o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, veja [utilizar grupos de recursos para gerir os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Para **versão**, selecione **V2**.

1.  Para **localização**, selecione a localização da fábrica de dados.

    Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (como o Armazenamento do Azure e a Base de Dados SQL do Azure) e as computações (como o HDInsight) utilizados pelo Data Factory podem estar noutras regiões.
1.  Selecione **criar**.


1.  Depois de concluída a criação, consulte a **fábrica de dados** página. Selecione o **criar e monitorizar** mosaico para iniciar a aplicação de IU do Data Factory num separador à parte.

    ![Iniciar a aplicação da IU da fábrica de dados](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, vai criar um serviço ligado do Databricks. Este serviço ligado contém as informações de ligação ao cluster do Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Criar um serviço ligado do Azure Databricks

1.  Sobre o **Vamos começar** página, mude para o **editar** separador no painel esquerdo.

    ![Editar o novo serviço ligado](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Selecione **conexões** na parte inferior da janela e, em seguida, selecione **+ novo**.
    
    ![Criar uma nova ligação](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Na **novo serviço ligado** janela, selecione **computação** \> **Azure Databricks**e, em seguida, selecione ** Continuar**.
    
    ![Especificar um serviço ligado do Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Na **novo serviço ligado** janela, conclua os seguintes passos:
    
    1.  Para **Nome**, introduza ***AzureDatabricks\_LinkedService***
    
    1.  Selecione a **Área de trabalho do Databricks** adequada na qual irá executar o Notebook

    1.  Em **Selecionar cluster**, selecione **Novo cluster de tarefas**
    
    1.  Em **Domínio/Região**, as informações devem ser preenchidas automaticamente

    1.  Para **Token de Acesso**, gere-o a partir da área de trabalho do Azure Databricks. Pode encontrar os passos [aqui](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  Para **versão do Cluster**, selecione **4.2** (com o Apache Spark 2.3.1, Scala 2.11)

    1.  Para este tutorial, em **Tipo de nó do cluster**, selecione **Standard\_D3\_v2** na categoria **Fins Gerais (HDD)**. 
    
    1.  Em **Funções de trabalho**, introduza **2**.
    
    1.  Selecione **Concluir**

        ![Concluir a criação do serviço ligado](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1.  Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline** no menu.

    ![Botões para criar um novo pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Crie um **parâmetro** para ser utilizado no **Pipeline**. Mais tarde, transmita este parâmetro à Atividade do Databricks Notebook. No pipeline vazio, clique no separador **Parâmetros** e, em seguida, em **Novo** e dê-lhe um nome no formato "**nome**".

    ![Criar um novo parâmetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Criar o parâmetro de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Na **atividades** caixa de ferramentas, expanda **Databricks**. Arrastar o **bloco de notas** atividade a partir do **atividades** caixa de ferramentas para a superfície de desenho do pipeline.

    ![Arrastar o Notebook para a superfície de desenho](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Nas propriedades para o **Databricks** **bloco de notas** janela de atividade na parte inferior, conclua os seguintes passos:

    a. Mude para o **Azure Databricks** separador.

    b. Selecione **AzureDatabricks\_LinkedService** (que criou no procedimento anterior).

    c. Mude para o separador **Definições**

    c. Navegue para selecionar um **caminho do Databricks Notebook**. Vamos criar um Notebook e especificar o caminho aqui. O Caminho do Notebook é obtido ao seguir os próximos passos.

       1. Iniciar a Área de trabalho do Azure Databricks

       1. Crie uma **Nova Pasta** na Área de Trabalho e denomine-a **adftutorial**.

          ![Criar uma nova pasta](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Criar um novo notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python) e vamos chamá-lo **mynotebook** sob **adftutorial** pasta, clique em **criar.**

          ![Criar um novo Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Definir as propriedades do novo Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. No Notebook recém-criado denominado "mynotebook", adicione o seguinte código:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Criar widgets para parâmetros](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Neste caso, o **Caminho do Notebook** é **/adftutorial/mynotebook**

1.  Volte à **ferramenta de criação da IU do Data Factory**. Navegue para o separador **Definições** em **Atividade do Notebook1**.

    a.  **Adicione o parâmetro** à atividade do Notebook. Vai utilizar o mesmo parâmetro que adicionou anteriormente ao **Pipeline**.

       ![Adicionar um parâmetro](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Nome do parâmetro como **entrada** e forneça o valor como expressão  **\@pipeline ().parameters.name**.

1.  Para validar o pipeline, selecione o **Validate** botão na barra de ferramentas. Para fechar a janela de validação, selecione o **\>\>** botão (seta para a direita).

    ![Validar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Selecione **publicar tudo**. A IU do Data Factory publica as entidades (serviços ligados e pipeline) no serviço Azure Data Factory.

    ![Publicar as novas entidades da fábrica de dados](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline

Selecione **acionador** na barra de ferramentas e, em seguida, selecione **acionar agora**.

![Selecionar o comando Acionar Agora](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A caixa de diálogo **Execução do Pipeline** pede-lhe o parâmetro **name**. Utilize **/path/filename** como parâmetro aqui. Clique em **Concluir**.

![Fornecer um valor para os parâmetros de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1.  Mude para o **Monitor** separador. Confirme que vê uma execução de pipeline. A criação de um cluster de trabalhos do Databricks, onde o Notebook vai ser executado, demora aproximadamente entre 5 a 8 minutos.

    ![Monitorizar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Selecione **Atualize** periodicamente para verificar o estado de execução do pipeline.

1.  Para ver as execuções de atividade associadas à execução do pipeline, selecione **ver execuções de atividades** no **ações** coluna.

    ![Ver as execuções de atividades](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Pode voltar a mudar para vista de execuções do pipeline, selecionando o **Pipelines** link na parte superior.

## <a name="verify-the-output"></a>Verificar a saída

Pode iniciar sessão na **Área de trabalho do Azure Databricks**, aceder a **Clusters** e ver o estado do **Trabalho** como *execução pendente, em execução ou terminado*.

![Ver o cluster de trabalhos e o trabalho](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Pode clicar no **Nome do trabalho** e navegar para ver mais detalhes. Numa execução bem-sucedida, pode validar os parâmetros transmitidos e o resultado do Python Notebook.

![Ver os detalhes de execução e o resultado](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Passos seguintes

O pipeline neste exemplo aciona uma atividade Databricks Notebook e transmite um parâmetro à mesma. Aprendeu a:

  - Criar uma fábrica de dados.

  - Criar um pipeline que utiliza uma atividade Databricks Notebook.

  - Acionar uma execução de pipeline.

  - Monitorizar a execução do pipeline.
