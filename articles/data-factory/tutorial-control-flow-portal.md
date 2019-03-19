---
title: Ramificação no pipeline do Azure Data Factory | Microsoft Docs
description: Saiba como controlar o fluxo de dados no Azure Data Factory através de atividades de ramificação e encadeamento.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 092a346d8303bb9e88a53b6fa529bb820635c554
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099547"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Atividades de ramificação e encadeamento num pipeline do Data Factory
Neste tutorial, vai criar um pipeline do Data Factory que demonstra algumas das funcionalidades de fluxo de controlo. Este pipeline cria uma cópia simples de um contentor do Armazenamento de Blobs do Azure para outro contentor na mesma conta de armazenamento. Se a atividade Copy tiver êxito, o pipeline envia detalhes da operação Copy bem-sucedida (por exemplo, a quantidade de dados escritos) num e-mail de êxito. Se a atividade Copy falhar, o pipeline envia detalhes da falha da cópia (por exemplo, a mensagem de erro) num e-mail de falha. Ao longo do tutorial, vai ver como passar os parâmetros.

Uma visão geral do cenário: ![Descrição geral](media/tutorial-control-flow-portal/overview.png)

Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade Copy e uma atividade Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar passagem de parâmetros e variáveis de sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Este tutorial utiliza o portal do Azure. Pode utilizar outros mecanismos para interagir com o Azure Data Factory. Veja "Inícios rápidos" no índice.

## <a name="prerequisites"></a>Pré-requisitos

* **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Conta de Armazenamento do Azure**. Utilize o armazenamento de blobs como arquivo de dados de **origem**. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter os passos para criar uma.
* **Base de Dados SQL do Azure**. Pode utilizar a base de dados como arquivo de dados **sink**. Se não tiver uma Base de Dados SQL do Azure, veja o artigo [Criar uma base de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md) para obter os passos para criar uma.

### <a name="create-blob-table"></a>Criar a tabela de blobs

1. Inicie o Bloco de notas. Copie o texto seguinte e guarde-o como **input.txt** no disco.

    ```
    John,Doe
    Jane,Doe
    ```
2. Utilize ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) e siga os passos abaixo: 
    1. Crie o contentor **adfv2branch** .
    2. Crie a pasta **input** no contentor **adfv2branch**.
    3. Carregue o ficheiro **input.txt** para o contentor.

## <a name="create-email-workflow-endpoints"></a>Criar pontos finais de fluxo de trabalho de e-mail
Para acionar o envio de um e-mail a partir do pipeline, utilize o [Logic Apps](../logic-apps/logic-apps-overview.md) para definir o fluxo de trabalho. Para obter detalhes sobre a criação de um fluxo de trabalho de Aplicação Lógica, veja [Como criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Fluxo de trabalho de e-mail de êxito 
Crie um fluxo de trabalho de Aplicação Lógica com o nome `CopySuccessEmail`. Definir o acionador do fluxo de trabalho como `When an HTTP request is received` e adicione uma ação de `Office 365 Outlook – Send an email`.

![Fluxo de trabalho de e-mail de êxito](media/tutorial-control-flow-portal/success-email-workflow.png)

Para o acionador do pedido, preencha o `Request Body JSON Schema` com o seguinte JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

O Pedido no Estruturador da Aplicação Lógica deve ter o seguinte aspeto: 

![Estruturador da Aplicação Lógica - pedido](media/tutorial-control-flow-portal/logic-app-designer-request.png)

Para a ação **Enviar E-mail**, personalize como pretende formatar o e-mail, utilizando as propriedades transmitidas no esquema JSON do Corpo do pedido. Segue-se um exemplo:

![Estruturador da Aplicação Lógica - ação enviar e-mail](media/tutorial-control-flow-portal/send-email-action-2.png)

Guarde o fluxo de trabalho. Anote o URL do pedido HTTP Post para o fluxo de trabalho de e-mail de êxito:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Fluxo de trabalho de e-mail de falha 
Siga os mesmos passos para criar outro fluxo de trabalho do Logic Apps de **CopyFailEmail**. No acionador do pedido, o `Request Body JSON schema` é o mesmo. Altere o formato do e-mail, como o `Subject`, para adaptar a um e-mail de falha. Segue-se um exemplo:

![Estruturador da Aplicação Lógica - fluxo de trabalho de e-mail de falha](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Guarde o fluxo de trabalho. Anote o URL do pedido HTTP Post para o fluxo de trabalho de e-mail de falha:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Agora, deve ter dois URLs de fluxo de trabalho:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
1. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-control-flow-portal/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/tutorial-control-flow-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos**, siga um destes passos:
     
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
        Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que são suportadas. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.      
8. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
   ![Home page da fábrica de dados](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a interface de utilizador (IU) do Azure Data Factory num separador à parte.


## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma atividade Copy e duas atividades Web. Vai criar o pipeline com as funcionalidades seguintes:

- Parâmetros para o pipeline que são acedidos por conjuntos de dados. 
- Atividade Web para invocar fluxos de trabalho de aplicações lógicas para enviar e-mails de êxito/falha. 
- Ligar uma atividade a outra (após êxito e falha)
- Utilizar um resultado de uma atividade como entrada para a atividade subsequente

1. Na página **introdução** da IU do Data Factory, clique no mosaico **Criar pipeline**.  

   ![Página Introdução](./media/tutorial-control-flow-portal/get-started-page.png) 
3. Na janela de propriedades do pipeline, mude para o separador **Parâmetros** e utilize o botão **Novo** para adicionar os três parâmetros seguintes do tipo Cadeia: sourceBlobContainer, sinkBlobContainer e receiver. 

    - **sourceBlobContainer** – parâmetro no pipeline consumido pelo conjunto de dados do blob de origem.
    - **sinkBlobContainer** – parâmetro no pipeline consumido pelo conjunto de dados do blob de sink.
    - **receiver** – este parâmetro é utilizado por duas atividades Web no pipeline que envia e-mails de êxito ou falha para o recetor cujo endereço de e-mail é especificado por este parâmetro.

   ![Menu Novo pipeline](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. Na caixa de ferramentas **Atividades**, expanda **Fluxo de Dados** e arraste e largue a atividade **Copy** na superfície de desenho do pipeline. 

   ![Arrastar e largar a atividade Copy](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. Na janela **Propriedades** da atividade **Copy**, na parte inferior, mude para o separador **Origem** e clique em **+ Novo**. Neste passo, vai criar um conjunto de dados de origem para a atividade Copy. 

   ![Conjunto de dados de origem](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**. 

   ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. Verá um **separador** novo com o nome **AzureBlob1**. Altere o nome do conjunto de dados para **SourceBlobDataset**.

   ![Definições gerais do conjunto de dados](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Mude para o separador **Ligação**, na janela **Propriedades**, e clique em Novo em **Serviço ligado**. Neste passo, vai criar um serviço ligado para ligar a sua conta de Armazenamento do Azure. 
    
   ![Ligação do conjunto de dados - novo serviço ligado](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Introduza **AzureStorageLinkedService** em **Nome**.
    2. Selecione a sua conta de Armazenamento do Azure em **Nome da conta de armazenamento**.
    3. Clique em **Guardar**.

   ![Novo serviço ligado do Armazenamento do Azure](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Introduza `@pipeline().parameters.sourceBlobContainer` para a pasta e `emp.txt` para o nome do ficheiro. Utilize o parâmetro de pipeline sourceBlobContainer para definir o caminho da pasta do conjunto de dados. 

   ![Definições do conjunto de dados de origem](./media/tutorial-control-flow-portal/source-dataset-settings.png)
13. Mude para o separador **Pipeline** (ou) clique no pipeline na vista de árvore. Confirme que **SourceBlobDataset** está selecionado em **Conjunto de Dados de Origem**. 

    ![Conjunto de dados de origem](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)
13. Na janela de propriedades, mude para o separador **Sink** e clique em **+ Novo** em **Conjunto de Dados de Sink**. Vai criar um conjunto de dados de sink para a atividade Copy neste passo de forma semelhante à forma como criou o conjunto de dados de origem. 

    ![Botão conjunto de dados de novo](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. Na janela **Novo Conjunto de Dados**, selecione **Armazenamento de Blobs do Azure** e clique em **Concluir**. 
15. Na página **Geral** das definições do conjunto de dados, introduza **SinkBlobDataset** em **Nome**.
16. Mude para o separador **Ligação** e siga os passos abaixo: 

    1. Selecione **AzureStorageLinkedService** em **Serviço ligado**.
    2. Introduza `@pipeline().parameters.sinkBlobContainer` para a pasta.
    1. Introduza `@CONCAT(pipeline().RunId, '.txt')` para o nome do ficheiro. A expressão utiliza o ID da execução de pipeline atual no nome do ficheiro. Para obter a lista de variáveis e expressões do sistema suportadas, veja [System variables](control-flow-system-variables.md) (Variáveis do sistema) e [Expression language](control-flow-expression-language-functions.md) (Linguagem de expressões).

        ![Definições do conjunto de dados de sink](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Mude para o separador **Pipeline**, na parte superior. Expanda **Geral**, na caixa de ferramentas **Atividades** e arraste e largue uma atividade **Web** na superfície de desenho do pipeline. Defina o nome da atividade como **SendSuccessEmailActivity**. A Atividade Web permite uma chamada para qualquer ponto final REST. Para obter mais informações sobre a atividade, veja [Atividade Web](control-flow-web-activity.md). Este pipeline utiliza uma Atividade Web para chamar o fluxo de trabalho de e-mail do Logic Apps. 

    ![Arrastar e largar a primeira atividade Web](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Mude para o separador **Definições**, a partir do separador **Geral** e siga os passos abaixo: 
    1. Em **URL**, especifique o URL do fluxo de trabalho da aplicação lógica que envia a mensagem de e-mail de êxito.  
    2. Selecione **POST** em **Método**. 
    3. Clique na ligação **+ Adicionar cabeçalho**, na secção **Cabeçalhos**. 
    4. Adicione um cabeçalho **Content-Type** e defina-o como **application/json**. 
    5. Especifique o JSON seguinte em **Corpo**. 

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        O corpo do e-mail contém as seguintes propriedades:

       - Mensagem – que transmite o valor de `@{activity('Copy1').output.dataWritten`. Acede a uma propriedade da atividade de cópia anterior e transmite o valor de dataWritten. Para o caso de falha, passe a saída de erro em vez de `@{activity('CopyBlobtoBlob').error.message`.
       - Nome da Fábrica de Dados – que transmite o valor de `@{pipeline().DataFactory}`. Esta é uma variável do sistema, que lhe permite aceder ao nome da fábrica de dados correspondente. Para obter uma lista de variáveis do sistema, veja o artigo [Variáveis do Sistema](control-flow-system-variables.md).
       - Nome do pipeline – que transmite o valor de `@{pipeline().Pipeline}`. Também se trata de uma variável do sistema, que lhe permite aceder ao nome do pipeline correspondente. 
       - Recetor – transmite o valor de "\@pipeline().parameters.receiver"). acedendo aos parâmetros do pipeline.
    
         ![Definições da primeira atividade Web](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Ligue a atividade **Copy** à atividade **Web** ao arrastar o botão verde junto à atividade Copy e largar na atividade Web. 

    ![Ligar a atividade Copy à primeira atividade Web](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Arraste e largue outra atividade **Web** da caixa de ferramentas Atividades para a superfície do estruturador do pipeline e defina o **nome** como **SendFailureEmailActivity**.

    ![Nome da segunda atividade Web](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Mude para o separador **Definições** e siga os passos abaixo:

    1. Em **URL**, especifique o URL do fluxo de trabalho da aplicação lógica que envia a mensagem de e-mail de falha.  
    2. Selecione **POST** em **Método**. 
    3. Clique na ligação **+ Adicionar cabeçalho**, na secção **Cabeçalhos**. 
    4. Adicione um cabeçalho **Content-Type** e defina-o como **application/json**. 
    5. Especifique o JSON seguinte em **Corpo**. 

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Definições da segunda atividade Web](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Selecione a atividade **Copy** no estruturador do pipeline, clique no botão **+->** e selecione **Erro**.  

    ![Definições da segunda atividade Web](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Arraste o botão **vermelho** junto à atividade Copy para a segunda atividade Web **SendFailureEmailActivity**. Pode mover as atividades de forma a que o aspeto do pipeline seja parecido com a imagem abaixo: 

    ![Pipeline completo com todas as atividades](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Para validar o pipeline, clique no botão **Validar**, na barra de ferramentas. Clique no botão **>>** para fechar a janela **Saída da Validação do Pipeline**.

    ![Validar o pipeline](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Para publicar entidades (conjuntos de dados, pipelines, etc.) no serviço Data Factory, selecione **Publicar Tudo**. Aguarde até ver a mensagem **Publicação com êxito**.

    ![Publicar](./media/tutorial-control-flow-portal/publish-button.png)
 
## <a name="trigger-a-pipeline-run-that-succeeds"></a>Acionar uma execução de pipeline bem-sucedida
1. Para **acionar** uma execução de pipeline, clique em **Acionar**, na barra de ferramentas, e clique em **Acionar Agora**. 

    ![Acionar uma execução de pipeline](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. Na janela **Execução de Pipeline**, siga os passos abaixo: 

    1. Introduza **adfv2branch/adftutorial/entrada** no parâmetro **sourceBlobContainer**. 
    2. Introduza **adftutorial/adfv2branch/output** no parâmetro **sinkBlobContainer**. 
    3. Introduza um **endereço de e-mail** do **recetor**. 
    4. Clique em **Concluir**

        ![Parâmetros de execução de pipeline](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Monitorizar a execução do pipeline bem-sucedida

1. Para monitorizar a execução do pipeline, mude para o separador **Monitorizar**, no lado esquerdo. Verá a execução de pipeline que acionou manualmente. Utilize o botão **Atualizar** para atualizar a lista. 
    
    ![Execução de pipeline bem-sucedida](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Para **ver as execuções de atividades** associadas a esta execução do pipeline, clique na primeira ligação na coluna **Ações**. Pode clicar em **Pipelines**, na parte superior, para regressar à vista anterior. Utilize o botão **Atualizar** para atualizar a lista. 

    ![Execuções de atividade](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Acionar uma execução de pipeline com falha
1. Mude para o separador **Editar**, no lado esquerdo. 
2. Para **acionar** uma execução de pipeline, clique em **Acionar**, na barra de ferramentas, e clique em **Acionar Agora**. 
3. Na janela **Execução de Pipeline**, siga os passos abaixo: 

    1. Introduza **adftutorial/dummy/input** no parâmetro **sourceBlobContainer**. Confirme que a pasta fictícia não existe no contentor adftutorial. 
    2. Introduza **adftutorial/dummy/output** para o parâmetro **sinkBlobContainer**. 
    3. Introduza um **endereço de e-mail** do **recetor**. 
    4. Clique em **Concluir**.

## <a name="monitor-the-failed-pipeline-run"></a>Monitorizar a execução de pipeline com falha

1. Para monitorizar a execução do pipeline, mude para o separador **Monitorizar**, no lado esquerdo. Verá a execução de pipeline que acionou manualmente. Utilize o botão **Atualizar** para atualizar a lista. 
    
    ![Execução de pipeline com falha](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Clique na ligação **Erro** da execução do pipeline para ver detalhes do erro. 

    ![Erro do pipeline](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Para **ver as execuções de atividades** associadas a esta execução do pipeline, clique na primeira ligação na coluna **Ações**. Utilize o botão **Atualizar** para atualizar a lista. Repare que a atividade Copy no pipeline falhou. A atividade Web conseguiu enviar com êxito o e-mail de falha ao recetor especificado. 

    ![Execuções de atividade](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Clique na ligação **Erro**, na coluna **Ações**, para ver detalhes do erro. 

    ![Erro da execução da atividade](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, executou os passos seguintes: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um serviço ligado do Armazenamento do Azure
> * Criar um conjunto de dados do Blob do Azure
> * Criar um pipeline que contém uma atividade de cópia e uma atividade da Web
> * Enviar saídas de atividades para atividades posteriores
> * Utilizar passagem de parâmetros e variáveis de sistema
> * Iniciar um execução de pipeline
> * Monitorizar o pipeline e execuções de atividades

Agora, pode avançar para a secção Conceitos para obter mais informações sobre o Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines e atividades](concepts-pipelines-activities.md)
