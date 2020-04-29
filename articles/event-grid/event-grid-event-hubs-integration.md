---
title: 'Tutorial: Enviar dados do Event Hubs para armazém de dados - Grelha de Eventos'
description: 'Tutorial: Descreve como usar a Grelha de Eventos Azure e os Centros de Eventos para migrar dados para um Armazém de Dados SQL. Usa uma Função Azure para recuperar um ficheiro Capture.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718194"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir big data para um armazém de dados
O Azure [Event Grid](overview.md) é um serviço inteligente de encaminhamento de eventos que lhe permite reagir a notificações (eventos) de apps e serviços. Por exemplo, pode desencadear uma Função Azure para processar dados do Event Hubs que foram capturados para um armazenamento de Blob Azure ou armazenamento de lagos de dados Azure, e migrar os dados para outros repositórios de dados. Esta amostra de integração de [Hubs de Eventos e Rede](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) de Eventos mostra como usar os Hubs de Eventos com a Grelha de Eventos para migrar sem problemas os dados capturados do Event Hubs desde o armazenamento de blob até um Armazém de Dados SQL.

![Descrição geral da aplicação](media/event-grid-event-hubs-integration/overview.png)

Este diagrama retrata o fluxo de trabalho da solução que constrói neste tutorial: 

1. Os dados enviados para um centro de eventos Azure são capturados num armazém de blob Azure.
2. Quando a captura de dados está completa, um evento é gerado e enviado para uma grelha de eventos Azure. 
3. A grelha de eventos reencaminha os dados deste evento para uma aplicação de função Azure.
4. A aplicação de função utiliza o URL blob nos dados do caso para recuperar a bolha do armazenamento. 
5. A aplicação de função migra os dados blob para um armazém de dados Azure SQL. 

Neste artigo, toma os seguintes passos:

> [!div class="checklist"]
> * Utilize um modelo de Gestor de Recursos Azure para implementar a infraestrutura: um hub de eventos, uma conta de armazenamento, uma aplicação de função, um armazém de dados SQL.
> * Crie uma tabela no armazém de dados.
> * Adicione código à aplicação de funções.
> * Subscreva o evento. 
> * Executar app que envia dados para o centro do evento.
> * Ver dados migrados em armazém de dados.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, tem de ter:

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Estúdio visual 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: .NET desenvolvimento de desktop, desenvolvimento Azure, ASP.NET e desenvolvimento web, desenvolvimento node.js e desenvolvimento python.
* Descarregue o projeto de [amostra EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para o seu computador.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Neste passo, você implementa a infraestrutura necessária com um [modelo de Gestor](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)de Recursos . Quando implementa o modelo, são criados os seguintes recursos:

* Centro de eventos com a função Capture ativada.
* Conta de armazenamento para os ficheiros capturados. 
* Plano de serviço de aplicativos para hospedar a app de funções
* Aplicação de funções para processar o evento
* SQL Server para alojar o armazém de dados
* SQL Data Warehouse para armazenar os dados migrados

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Lançar Azure Cloud Shell no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione o botão **Cloud Shell** na parte superior.

    ![Portal do Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Você vê a Cloud Shell aberta na parte inferior do navegador.

    ![Cloud Shell,](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Na Cloud Shell, se vir uma opção para selecionar entre **Bash** e **PowerShell,** selecione **Bash**. 
5. Se estiver a utilizar a Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando o **armazenamento Create**. A Azure Cloud Shell requer uma conta de armazenamento Azure para armazenar alguns ficheiros. 

    ![Criar armazenamento para casca de nuvem](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Espere até que a Cloud Shell seja inicializada. 

    ![Criar armazenamento para casca de nuvem](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

1. Crie um grupo de recursos Azure executando o seguinte comando CLI: 
    1. Copiar e colar o seguinte comando na janela Cloud Shell

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Especificar um nome para o **grupo de recursos**
    2. Prima **ENTER**. 

        Segue-se um exemplo:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implementar todos os recursos mencionados na secção anterior (hub de eventos, conta de armazenamento, aplicação de funções, armazém de dados SQL) executando o seguinte comando CLI: 
    1. Copie e cole o comando na janela Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copiar o comando para a Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Especificar valores para as seguintes entidades:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nome do centro de eventos. 
        3. Nome para o centro do evento. Pode deixar o valor tal como está (hubdatamigração).
        4. Nome para o servidor SQL.
        5. Nome do utilizador SQL e palavra-passe. 
        6. Nome para o armazém de dados SQL
        7. Nome da conta de armazenamento. 
        8. Nome para a aplicação de funções. 
    3.  Pressione **ENTER** na janela Cloud Shell para executar o comando. Este processo pode demorar algum tempo, uma vez que está a criar um monte de recursos. No resultado do comando, certifique-se de que não houve falhas. 
    

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Na Casca de Nuvem Azure, mude para o modo PowerShell. Selecione seta para baixo no canto superior esquerdo da Bluee Cloud Shell e selecione **PowerShell**.

    ![Mudar para PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Crie um grupo de recursos Azure executando o seguinte comando: 
    1. Copie e cole o seguinte comando na janela Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Especifique um nome para o **grupo de recursos**.
    3. Prima ENTER. 
3. Implementar todos os recursos mencionados na secção anterior (hub de eventos, conta de armazenamento, aplicação de funções, armazém de dados SQL) executando o seguinte comando:
    1. Copie e cole o comando na janela Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copiar o comando para a Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Especificar valores para as seguintes entidades:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nome do centro de eventos. 
        3. Nome para o centro do evento. Pode deixar o valor tal como está (hubdatamigração).
        4. Nome para o servidor SQL.
        5. Nome do utilizador SQL e palavra-passe. 
        6. Nome para o armazém de dados SQL
        7. Nome da conta de armazenamento. 
        8. Nome para a aplicação de funções. 
    3.  Pressione **ENTER** na janela Cloud Shell para executar o comando. Este processo pode demorar algum tempo, uma vez que está a criar um monte de recursos. No resultado do comando, certifique-se de que não houve falhas. 

### <a name="close-the-cloud-shell"></a>Feche a Nuvem Shell 
Feche a casca da nuvem selecionando o botão **Cloud Shell** no portal (ou) botão **X** no canto superior direito da janela Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verifique se os recursos são criados

1. No portal Azure, selecione **Grupos de Recursos** no menu esquerdo. 
2. Filtre a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione o seu grupo de recursos na lista.

    ![Selecione o seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confirme que vê os seguintes recursos no grupo de recursos:

    ![Recursos no grupo de recursos](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse
Crie uma tabela no seu armazém de dados executando o script [CreateDataWarehouseTable.sql.](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) Para executar o script, pode utilizar o Visual Studio ou o Editor de Consulta no portal. Os seguintes passos mostram-lhe como usar o Editor de Consulta: 

1. Na lista de recursos do grupo de recursos, selecione o seu armazém de dados SQL. 
2. Na página do armazém de dados SQL, selecione **O editor de Consulta (pré-visualização)** no menu esquerdo. 

    ![Página de armazém de dados SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Introduza o nome do **utilizador** e **palavra-passe** para o servidor SQL e selecione **OK**. 

    ![Autenticação do SQL Server](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Na janela de consulta, copie e execute o seguinte script SQL: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![Executar consulta SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Mantenha este separador ou janela aberto para que possa verificar se os dados são criados no final do tutorial. 


## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de Funções do Azure

1. Inicie o Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo.sln** que descarregou do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos.
3. No Explorador de Soluções, clique com o botão direito do rato em **FunctionEGDWDumper** e selecione **Publicar**.

   ![Publicar aplicação de funções](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se vir o seguinte ecrã, selecione **Iniciar**. 

   ![Comece a publicar botão](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na **página-alvo de escolha,** selecione a opção **Select existente** e selecione **Criar Perfil**. 

   ![Escolher um destino de publicação](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na página do Serviço de Aplicações, selecione a sua **subscrição Azure,** selecione a aplicação de **funções** no seu grupo de recursos e selecione **OK**. 

   ![Página de serviço de aplicativos](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecione publicar](media/event-grid-event-hubs-integration/select-publish.png)

Depois de publicar a função, está pronto para subscrever o evento.

## <a name="subscribe-to-the-event"></a>Subscrever o evento

1. Num novo separador ou nova janela de um navegador web, navegue para o [portal Azure.](https://portal.azure.com)
2. No portal Azure, selecione **Grupos de Recursos** no menu esquerdo. 
3. Filtre a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione o seu grupo de recursos na lista.

    ![Selecione o seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selecione o plano de Serviço de Aplicações na lista. 
5. Na página do Plano de Serviço de Aplicações, selecione **Apps** no menu esquerdo e selecione a aplicação de funções. 

    ![Selecione a sua aplicação de funções](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expanda a aplicação de função, expanda funções e, em seguida, selecione a sua função. 

    ![Selecionar a função do Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. **Selecione Adicionar a subscrição da Grelha** de Eventos na barra de ferramentas. 
8. Na página de **Subscrição da Grelha** de Eventos Criar Eventos, faça as seguintes ações: 
    1. Na secção **TÓPICO DETALHES,** faça as seguintes ações:
        1. Selecione a sua subscrição do Azure.
        2. Selecione o grupo de recursos Azure.
        3. Selecione o espaço de nome sinuoso do Event Hubs.
    2. Na página DETALHES DE SUBSCRIÇÃO DO **EVENTO,** introduza um nome para a subscrição (por exemplo: captureEventSub) e selecione **Criar**. 

        ![Criar subscrição da Grelha de Eventos](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados
Acabou de configurar o seu hub de eventos, o armazém de dados SQL, aplicação de funções do Azure e a subscrição do evento. Antes de executar uma aplicação que gera dados para o hub de eventos, tem de configurar alguns valores.

1. No portal Azure, navegue para o seu grupo de recursos como fez anteriormente. 
2. Selecione o espaço de nome sinuoso do Event Hubs.
3. Na página **Nomespace Do Evento Hubs,** selecione políticas de **acesso partilhado** no menu esquerdo.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 
5. Selecione o botão de cópia ao lado da caixa de texto primária de **ligação.** 

    ![Fio de ligação para o espaço de nome do centro de eventos](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Volte para a sua solução de Estúdio Visual. 
2. No projeto WindTurbineDataGenerator, abra **program.cs**.
5. Substitua os dois valores constantes. Utilize o valor copiado para **EventHubConnectionString**. Utilize **hubdatamigration**, o nome do hub de eventos. Se usou um nome diferente para o centro do evento, especifique esse nome. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilar a solução. Executar a aplicação **WindTurbineGenerator.exe.** 
7. Após alguns minutos, consulte a tabela no seu armazém de dados para os dados migrados.

    ![Resultados da consulta](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dados do evento gerados pelo centro de eventos
O Event Grid distribui dados de eventos para os subscritores. O exemplo seguinte mostra os dados do evento gerados quando o streaming de dados através de um centro de eventos é capturado numa bolha. Em particular, `fileUrl` note `data` que a propriedade no objeto aponta para a bolha no armazenamento. A aplicação de funções utiliza este URL para recuperar o ficheiro blob com dados capturados.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as diferenças dos serviços de mensagens do Azure, veja [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para obter uma introdução à Recolha dos Hubs de Eventos, veja [Ativar Recolha dos Hubs de Eventos com o portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, veja [Exemplo do Event Grid e da Recolha dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
