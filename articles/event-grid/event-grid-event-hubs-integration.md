---
title: 'Tutorial: enviar dados de hubs de eventos para a grade de eventos data warehouse'
description: 'Tutorial: descreve como usar a grade de eventos do Azure e os hubs de eventos para migrar dados para um SQL Data Warehouse. Ele usa uma função do Azure para recuperar um arquivo de captura.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718194"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: transmitir Big Data para um data warehouse
A [grade de eventos](overview.md) do Azure é um serviço de roteamento de eventos inteligente que permite reagir a notificações (eventos) de aplicativos e serviços. Por exemplo, ele pode disparar uma função do Azure para processar dados de hubs de eventos que foram capturados em um armazenamento de BLOBs do Azure ou Azure Data Lake Storage e migrar os dados para outros repositórios de dados. Este [exemplo de integração de grade de eventos e hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) de eventos mostra como usar os hubs de eventos com a grade de eventos para migrar diretamente os dados capturados dos hubs de eventos do armazenamento de BLOBs para um SQL data warehouse.

![Descrição geral da aplicação](media/event-grid-event-hubs-integration/overview.png)

Este diagrama ilustra o fluxo de trabalho da solução que você cria neste tutorial: 

1. Os dados enviados para um hub de eventos do Azure são capturados em um armazenamento de BLOBs do Azure.
2. Quando a captura de dados é concluída, um evento é gerado e enviado a uma grade de eventos do Azure. 
3. A grade de eventos encaminha esses dados de evento para um aplicativo de funções do Azure.
4. O aplicativo de funções usa a URL do blob nos dados do evento para recuperar o blob do armazenamento. 
5. O aplicativo de funções migra os dados de BLOB para um Azure SQL data warehouse. 

Neste artigo, você executará as seguintes etapas:

> [!div class="checklist"]
> * Use um modelo de Azure Resource Manager para implantar a infraestrutura: um hub de eventos, uma conta de armazenamento, um aplicativo de funções, um data warehouse SQL.
> * Crie uma tabela no data warehouse.
> * Adicione código ao aplicativo de funções.
> * Assine o evento. 
> * Execute o aplicativo que envia dados para o Hub de eventos.
> * Exibir dados migrados no data warehouse.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, tem de ter:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual studio 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de desktops .net, desenvolvimento do Azure, desenvolvimento de ASP.net e Web, desenvolvimento de Node. js e desenvolvimento em Python.
* Baixe o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) em seu computador.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Nesta etapa, você implanta a infraestrutura necessária com um [modelo do Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando você implanta o modelo, os seguintes recursos são criados:

* Hub de eventos com o recurso de captura habilitado.
* Conta de armazenamento para os arquivos capturados. 
* Plano do serviço de aplicativo para hospedar o aplicativo de funções
* Aplicação de funções para processar o evento
* SQL Server para alojar o armazém de dados
* SQL Data Warehouse para armazenar os dados migrados

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Iniciar Azure Cloud Shell no portal do Azure

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione **Cloud Shell** botão na parte superior.

    ![Portal do Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Você verá o Cloud Shell aberto na parte inferior do navegador.

    ![Cloud Shell,](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Na Cloud Shell, se você vir uma opção para selecionar entre o **bash** e o **PowerShell**, selecione **bash**. 
5. Se você estiver usando o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **criar armazenamento**. Azure Cloud Shell requer uma conta de armazenamento do Azure para armazenar alguns arquivos. 

    ![Criar armazenamento para Cloud Shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Aguarde até que o Cloud Shell seja inicializado. 

    ![Criar armazenamento para Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

1. Crie um grupo de recursos do Azure executando o seguinte comando da CLI: 
    1. Copie e cole o seguinte comando na janela de Cloud Shell

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Especifique um nome para o **grupo de recursos**
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
2. Implante todos os recursos mencionados na seção anterior (Hub de eventos, conta de armazenamento, aplicativo de funções, SQL data warehouse) executando o seguinte comando da CLI: 
    1. Copie e cole o comando na janela Cloud Shell. Como alternativa, talvez você queira copiar/colar em um editor de sua escolha, definir valores e, em seguida, copiar o comando para o Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Especifique valores para as seguintes entidades:
        1. Nome do grupo de recursos que você criou anteriormente.
        2. Nome do namespace do hub de eventos. 
        3. Nome do hub de eventos. Você pode deixar o valor como está (hubdatamigration).
        4. Nome para o SQL Server.
        5. Nome do usuário e da senha do SQL. 
        6. Nome para o data warehouse do SQL
        7. Nome da conta de armazenamento. 
        8. Nome para o aplicativo de funções. 
    3.  Pressione **Enter** na janela de Cloud Shell para executar o comando. Esse processo pode demorar um pouco, já que você está criando um monte de recursos. No resultado do comando, verifique se não houve falhas. 
    

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. No Azure Cloud Shell, alterne para o modo PowerShell. Selecione a seta para baixo no canto superior esquerdo de Azure Cloud Shell e selecione **PowerShell**.

    ![Alternar para o PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Crie um grupo de recursos do Azure executando o seguinte comando: 
    1. Copie e cole o comando a seguir na janela Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Especifique um nome para o **grupo de recursos**.
    3. Pressione ENTER. 
3. Implante todos os recursos mencionados na seção anterior (Hub de eventos, conta de armazenamento, aplicativo de funções, SQL data warehouse) executando o seguinte comando:
    1. Copie e cole o comando na janela Cloud Shell. Como alternativa, talvez você queira copiar/colar em um editor de sua escolha, definir valores e, em seguida, copiar o comando para o Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Especifique valores para as seguintes entidades:
        1. Nome do grupo de recursos que você criou anteriormente.
        2. Nome do namespace do hub de eventos. 
        3. Nome do hub de eventos. Você pode deixar o valor como está (hubdatamigration).
        4. Nome para o SQL Server.
        5. Nome do usuário e da senha do SQL. 
        6. Nome para o data warehouse do SQL
        7. Nome da conta de armazenamento. 
        8. Nome para o aplicativo de funções. 
    3.  Pressione **Enter** na janela de Cloud Shell para executar o comando. Esse processo pode demorar um pouco, já que você está criando um monte de recursos. No resultado do comando, verifique se não houve falhas. 

### <a name="close-the-cloud-shell"></a>Feche o Cloud Shell 
Feche o Cloud Shell selecionando o botão **Cloud Shell** no portal (ou) botão **X** no canto superior direito da janela Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verificar se os recursos foram criados

1. No portal do Azure, selecione **grupos de recursos** no menu à esquerda. 
2. Filtre a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione o grupo de recursos na lista.

    ![Selecione seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confirme que você vê os seguintes recursos no grupo de recursos:

    ![Recursos no grupo de recursos](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse
Crie uma tabela no data warehouse executando o script [Createdatawarehousetable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) . Para executar o script, você pode usar o Visual Studio ou o editor de consultas no Portal. As etapas a seguir mostram como usar o editor de consultas: 

1. Na lista de recursos no grupo de recursos, selecione o SQL data warehouse. 
2. Na página SQL data warehouse, selecione **Editor de consultas (visualização)** no menu à esquerda. 

    ![Página de data warehouse do SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Insira o nome do **usuário** e a **senha** para o SQL Server e selecione **OK**. 

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
5. Mantenha essa guia ou janela aberta para que você possa verificar se os dados foram criados no final do tutorial. 


## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de Funções do Azure

1. Inicie o Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo. sln** que você baixou do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos.
3. No Explorador de Soluções, clique com o botão direito do rato em **FunctionEGDWDumper** e selecione **Publicar**.

   ![Publicar aplicação de funções](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se você vir a tela a seguir, selecione **Iniciar**. 

   ![Botão iniciar publicação](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na página **escolher um destino de publicação** , selecione a opção **selecionar existente** e selecione **Criar perfil**. 

   ![Escolher um destino de publicação](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na página Serviço de aplicativo, selecione sua **assinatura do Azure**, selecione o **aplicativo de funções** em seu grupo de recursos e selecione **OK**. 

   ![Página do serviço de aplicativo](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecione publicar](media/event-grid-event-hubs-integration/select-publish.png)

Depois de publicar a função, está pronto para subscrever o evento.

## <a name="subscribe-to-the-event"></a>Subscrever o evento

1. Em uma nova guia ou nova janela de um navegador da Web, navegue até a [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **grupos de recursos** no menu à esquerda. 
3. Filtre a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione o grupo de recursos na lista.

    ![Selecione seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selecione o plano do serviço de aplicativo na lista. 
5. Na página plano do serviço de aplicativo, selecione **aplicativos** no menu à esquerda e selecione o aplicativo de funções. 

    ![Selecione seu aplicativo do Functions](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expanda o aplicativo de função, expanda funções e selecione sua função. 

    ![Selecionar a função do Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Selecione **Adicionar assinatura de grade de eventos** na barra de ferramentas. 
8. Na página **criar assinatura da grade de eventos** , execute as seguintes ações: 
    1. Na seção **detalhes do tópico** , execute as seguintes ações:
        1. Selecione a sua subscrição do Azure.
        2. Selecione o grupo de recursos do Azure.
        3. Selecione o namespace de hubs de eventos.
    2. Na página **detalhes da assinatura do evento** , insira um nome para a assinatura (por exemplo: captureEventSub) e selecione **criar**. 

        ![Criar assinatura da grade de eventos](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados
Acabou de configurar o seu hub de eventos, o armazém de dados SQL, aplicação de funções do Azure e a subscrição do evento. Antes de executar uma aplicação que gera dados para o hub de eventos, tem de configurar alguns valores.

1. No portal do Azure, navegue até o seu grupo de recursos como fazia anteriormente. 
2. Selecione o namespace de hubs de eventos.
3. Na página **namespace de hubs de eventos** , selecione **políticas de acesso compartilhado** no menu à esquerda.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 
5. Selecione o botão copiar ao lado da caixa de texto **cadeia de conexão-chave primária** . 

    ![Cadeia de conexão para o namespace do hub de eventos](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Volte para sua solução do Visual Studio. 
2. No projeto WindTurbineDataGenerator, abra **program.cs**.
5. Substitua os dois valores constantes. Utilize o valor copiado para **EventHubConnectionString**. Utilize **hubdatamigration**, o nome do hub de eventos. Se você usou um nome diferente para o Hub de eventos, especifique esse nome. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compile a solução. Execute o aplicativo **WindTurbineGenerator. exe** . 
7. Após alguns minutos, consulte a tabela no seu armazém de dados para os dados migrados.

    ![Resultados da consulta](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dados de evento gerados pelo hub de eventos
O Event Grid distribui dados de eventos para os subscritores. O exemplo a seguir mostra os dados de evento gerados quando o streaming de dados por meio de um hub de eventos é capturado em um blob. Em particular, observe que a propriedade `fileUrl` no objeto `data` aponta para o blob no armazenamento. O aplicativo de funções usa essa URL para recuperar o arquivo de blob com dados capturados.

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
