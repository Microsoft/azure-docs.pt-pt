---
title: Executar um Caderno databricks com a atividade
description: Saiba como pode utilizar a Atividade do Databricks Notebook num Azure Data Factory para executar um Databricks Notebook no cluster de tarefas do Databricks.
ms.service: data-factory
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: 20253954035798187f28c18c8207c114d27bc9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374087"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Executar um Databricks Notebook com a Atividade do Databricks Notebook no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, utiliza o portal do Azure para criar um pipeline do Azure Data Factory que executa um Databricks Notebook no cluster de tarefas do Databricks. Também transmite os parâmetros do Azure Data Factory ao Databricks Notebook durante a execução.

Vai executar os seguintes passos neste tutorial:

  - Criar uma fábrica de dados.

  - Crie um pipeline que utilize a atividade do Databricks Notebook.

  - Acionar uma execução de pipeline.

  - Monitorizar a execução do pipeline.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Pré-requisitos

  - Espaço de **trabalho Azure Databricks**. [Crie um espaço de trabalho Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) ou use um existente. Crie um Python Notebook na sua área de trabalho do Azure Databricks. Em seguida, execute o Notebook e transmita os parâmetros ao mesmo através do Azure Data Factory.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1.  Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.

1.  Selecione **Criar um recurso** no menu esquerdo, selecione **Análise** e, em seguida, selecione **Data Factory**.

    ![Criar uma nova fábrica de dados](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  No painel **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** em **Nome**.

    O nome da fábrica de dados Azure deve ser *globalmente único.* Se vir o seguinte erro, altere o nome da fábrica de dados. (Por exemplo, utilize **\<yourname\> a ADFTutorialDataFactory).** Para regras de nomenclatura de artefactos do Data Factory, veja o artigo [Data Factory – Regras de Nomenclatura](./naming-rules.md).

    ![Indicar um nome para a nova fábrica de dados](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Em **Subscrição**, selecione a sua subscrição do Azure na qual pretende criar a fábrica de dados.

1.  Em **Grupo de Recursos**, efetue um destes passos:
    
    - Selecione **Utilize o grupo de** recursos existente na lista de drop-down.
    
    - Selecione **Criar novo** e insira o nome de um grupo de recursos.

    Alguns dos passos deste início rápido pressupõem que utiliza o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).

1.  Em **Versão**, selecione **V2**.

1.  Em **Localização**, selecione a localização para a fábrica de dados.

    Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (como o Armazenamento do Azure e a Base de Dados SQL do Azure) e as computações (como o HDInsight) utilizados pelo Data Factory podem estar noutras regiões.
1.  Selecione **Criar**.


1.  Após concluir a criação, verá a página **Fábrica de dados**. Selecione o mosaico **Criar e Monitorizar** para iniciar a aplicação de IU do Azure Data Factory num separador à parte.

    ![Iniciar a aplicação da IU da fábrica de dados](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Criar serviços ligados

Nesta secção, vai criar um serviço ligado do Databricks. Este serviço ligado contém as informações de ligação ao cluster do Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Criar um serviço ligado do Azure Databricks

1.  Na página **Introdução**, mude para o separador **Editar**, no painel esquerdo.

    ![Editar o novo serviço ligado](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Selecione **Ligações**, na parte inferior da janela, e selecione **+ Novo**.
    
    ![Criar uma nova ligação](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Na janela **Novo Serviço Ligado**, selecione **Computação** \> **Azure Databricks** e, em seguida, **Continuar**.
    
    ![Especificar um serviço ligado do Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Na janela **Novo Serviço Ligado**, conclua os passos abaixo:
    
    1.  Para **nome**, insira **_O AzureDatabricks \_ LinkedService_**
    
    1.  Selecione a **Área de trabalho do Databricks** adequada na qual irá executar o Notebook

    1.  Em **Selecionar cluster**, selecione **Novo cluster de tarefas**
    
    1.  Em **Domínio/Região**, as informações devem ser preenchidas automaticamente

    1.  Para **Token de Acesso**, gere-o a partir da área de trabalho do Azure Databricks. Pode encontrar os passos [aqui](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  Para **a versão Cluster**, selecione **4.2** (com Apache Spark 2.3.1, Scala 2.11)

    1.  Para este tutorial, em **Tipo de nó do cluster**, selecione **Standard\_D3\_v2** na categoria **Fins Gerais (HDD)**. 
    
    1.  Em **Funções de trabalho**, introduza **2**.
    
    1.  Selecione **Acabamento**

        ![Concluir a criação do serviço ligado](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1.  Selecione o **+** botão (mais) e, em seguida, selecione **Pipeline** no menu.

    ![Botões para criar um novo pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Crie um **parâmetro** para ser utilizado no **Pipeline**. Mais tarde, transmita este parâmetro à Atividade do Databricks Notebook. No pipeline vazio, clique no separador **Parâmetros** e, em seguida, em **Novo** e dê-lhe um nome no formato "**nome**".

    ![Criar um novo parâmetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Criar o parâmetro de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Na caixa de ferramentas **Atividades**, expanda **Databricks**. Arraste a atividade do **Notebook** da caixa de ferramentas **Atividades** para a superfície de desenho do pipeline.

    ![Arrastar o Notebook para a superfície de desenho](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Nas propriedades da janela de atividade do **Databricks** **Notebook** na parte inferior, conclua os seguintes passos:

    a. Mude para o separador **Azure Databricks**.

    b. Selecione **AzureDatabricks\_LinkedService** (criado no procedimento anterior).

    c. Mudar para o **separador Definições**

    c. Navegue para selecionar um **caminho do Databricks Notebook**. Vamos criar um Notebook e especificar o caminho aqui. O Caminho do Notebook é obtido ao seguir os próximos passos.

       1. Iniciar a Área de trabalho do Azure Databricks

       1. Crie uma **Nova Pasta** na Área de Trabalho e denomine-a **adftutorial**.

          ![Criar uma nova pasta](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Crie um novo caderno](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), vamos chamá-lo **de mynotebook** sob pasta **adftutorial,** clique em **Criar.**

          ![Criar um novo Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Definir as propriedades do novo Notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. No Notebook recém-criado denominado "mynotebook&quot;, adicione o seguinte código:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text(&quot;input&quot;, &quot;&quot;,&quot;")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Criar widgets para parâmetros](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. Neste caso, o **Caminho do Notebook** é **/adftutorial/mynotebook**

1.  Volte à **ferramenta de criação da IU do Data Factory**. Navegue para o separador **Definições** em **Atividade do Notebook1**.

    a.  **Adicione o parâmetro** à atividade do Notebook. Vai utilizar o mesmo parâmetro que adicionou anteriormente ao **Pipeline**.

       ![Adicionar um parâmetro](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Nomeie o parâmetro como **entrada** e forneça o valor como **\@ pipeline de expressão().parâmetros.name**.

1.  Para validar o pipeline, selecione o botão **Validar** na barra de ferramentas. Para fechar a janela de validação, selecione o **\>\>** botão (seta direita).

    ![Validar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Selecione **Publicar Tudo**. A IU do Data Factory publica as entidades (serviços ligados e pipeline) no serviço Azure Data Factory.

    ![Publicar as novas entidades da fábrica de dados](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Acionar uma execução de pipeline

Selecione **Acionar** na barra de ferramentas e, em seguida, selecione **Acionar Agora**.

![Selecionar o comando Acionar Agora](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A caixa de diálogo **Execução do Pipeline** pede-lhe o parâmetro **name**. Utilize **/path/filename** como parâmetro aqui. Clique **em Terminar.**

![Fornecer um valor para os parâmetros de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1.  Mude para o **separador Monitor.** Confirme que vê um oleoduto a funcionar. A criação de um cluster de trabalhos do Databricks, onde o Notebook vai ser executado, demora aproximadamente entre 5 a 8 minutos.

    ![Monitorizar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Selecione **Atualizar** periodicamente para verificar o estado da execução do pipeline.

1.  Para ver as execuções de atividades associadas à execução do pipeline, selecione **Ver Execuções de Atividades**, na coluna **Ações**.

    ![Ver as execuções de atividades](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Pode selecionar a ligação **Pipelines**, na parte superior, para regressar à vista de execuções do pipeline.

## <a name="verify-the-output"></a>Verificar a saída

Pode iniciar sessão na **Área de trabalho do Azure Databricks**, aceder a **Clusters** e ver o estado da **Tarefa** como *execução pendente, em execução ou terminada*.

![Ver o cluster de trabalhos e o trabalho](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Pode clicar no **Nome do trabalho** e navegar para ver mais detalhes. Numa execução bem-sucedida, pode validar os parâmetros transmitidos e o resultado do Python Notebook.

![Ver os detalhes de execução e o resultado](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Passos seguintes

O pipeline neste exemplo aciona uma atividade Databricks Notebook e transmite um parâmetro à mesma. Aprendeu a:

  - Criar uma fábrica de dados.

  - Criar um pipeline que utiliza uma atividade Databricks Notebook.

  - Acionar uma execução de pipeline.

  - Monitorizar a execução do pipeline.