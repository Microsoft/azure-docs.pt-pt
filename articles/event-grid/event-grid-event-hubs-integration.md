---
title: Enviar o armazém de dados para dados de Hubs de eventos - Event Grid
description: Descreve como utilizar o Azure Event Grid e os Hubs de eventos para migrar dados para um SQL Data Warehouse. Ele usa uma função do Azure para recuperar um arquivo de captura.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: c2c49563bf505ce70c4900c6c0a8e41c0f6ac9c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176621"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir macrodados em fluxo para um armazém de dados
Azure [Event Grid](overview.md) é um serviço de encaminhamento de eventos inteligente que permite-lhe reagir a notificações (eventos) de aplicações e serviços. Por exemplo, ele pode acionar uma função do Azure para processar dados de Hubs de eventos que captura a um armazenamento de Blobs do Azure ou o armazenamento do Azure Data Lake e migrar os dados para outros repositórios de dados. Isso [exemplo de integração do Hubs de eventos e o Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra-lhe como utilizar os Hubs de eventos com o Event Grid migrar dados de Hubs de eventos capturados do armazenamento de BLOBs para um SQL Data Warehouse.

![Descrição geral da aplicação](media/event-grid-event-hubs-integration/overview.png)

Este diagrama ilustra o fluxo de trabalho da solução que criar neste tutorial: 

1. Dados enviados para um hub de eventos do Azure são capturados num armazenamento de Blobs do Azure.
2. Quando a captura de dados estiver concluída, um evento é gerado e enviado para uma grelha de eventos do Azure. 
3. A grelha de eventos por sua vez encaminha esses dados de eventos a uma aplicação de função do Azure.
4. A aplicação de funções utiliza o URL do blob dos dados de eventos para obter o blob do armazenamento. 
5. A aplicação de funções migra os dados de blob para um armazém de dados SQL do Azure. 

Neste artigo, siga os passos seguintes:

> [!div class="checklist"]
> * Utilizar um modelo Azure Resource Manager para implementar a infraestrutura: um hub de eventos, uma conta de armazenamento, uma aplicação de funções, um SQL data warehouse.
> * Crie uma tabela no armazém de dados.
> * Adicione o código para a aplicação de funções.
> * Subscreva o evento. 
> * Execute a aplicação que envia dados para o hub de eventos.
> * Ver os dados migrados no armazém de dados.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, tem de ter:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual studio 2017 Versão 15.3.2 ou superior](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de ambiente de trabalho .NET, desenvolvimento do Azure, desenvolvimento de ASP.NET e web, desenvolvimento de Node.js e desenvolvimento de Python.
* Transfira o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para o seu computador.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Neste passo, vai implementar a infraestrutura necessária com um [modelo do Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando implementar o modelo, são criados os seguintes recursos:

* Hub de eventos com a funcionalidade de captura ativada.
* Conta de armazenamento para os ficheiros capturadas. 
* Plano do serviço de aplicações para alojar a aplicação de funções
* Aplicação de funções para processar o evento
* SQL Server para alojar o armazém de dados
* SQL Data Warehouse para armazenar os dados migrados

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Inicie o Azure Cloud Shell no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione **Cloud Shell** botão na parte superior.

    ![Portal do Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Verá que o Cloud Shell é aberto na parte inferior do navegador.

    ![Cloud Shell,](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. No Cloud Shell, se vir uma opção para selecionar entre **Bash** e **PowerShell**, selecione **Bash**. 
5. Se estiver a utilizar o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **criar armazenamento**. O Azure Cloud Shell requer uma conta de armazenamento do Azure para armazenar alguns ficheiros. 

    ![Criar armazenamento para cloud shell](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Aguarde até que o Cloud Shell é inicializado. 

    ![Criar armazenamento para cloud shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

1. Crie um grupo de recursos do Azure ao executar o seguinte comando da CLI: 
    1. Copie e cole o seguinte comando na janela do Cloud Shell

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
2. Implemente todos os recursos mencionados na secção anterior (hub de eventos, conta de armazenamento, aplicação de funções, o SQL data warehouse) ao executar o seguinte comando da CLI: 
    1. Copie e cole o comando na janela do Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copie o comando para o Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Especifique valores para as entidades a seguir:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nomes do hub de eventos. 
        3. Nome para o hub de eventos. Pode deixar o valor, como é (hubdatamigration).
        4. Nome do SQL Server.
        5. Nome do utilizador SQL e a palavra-passe. 
        6. Nome para o SQL data warehouse
        7. Nome da conta de armazenamento. 
        8. Nome da aplicação de função. 
    3.  Prima **ENTER** na janela do Cloud Shell para executar o comando. Este processo poderá demorar algum tempo, como está criando uma série de recursos. No resultado do comando, certifique-se de que tenham sido sem falhas. 
    

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. No Azure Cloud Shell, mude para o modo do PowerShell. Selecione a seta no canto superior esquerdo do Azure Cloud Shell e selecione **PowerShell**.

    ![Mude para o PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Crie um grupo de recursos do Azure ao executar o seguinte comando: 
    1. Copie e cole o seguinte comando na janela do Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Especifique um nome para o **grupo de recursos**.
    3. Prima ENTER. 
3. Implemente todos os recursos mencionados na secção anterior (hub de eventos, conta de armazenamento, aplicação de funções, o SQL data warehouse) ao executar o seguinte comando:
    1. Copie e cole o comando na janela do Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copie o comando para o Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Especifique valores para as entidades a seguir:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nomes do hub de eventos. 
        3. Nome para o hub de eventos. Pode deixar o valor, como é (hubdatamigration).
        4. Nome do SQL Server.
        5. Nome do utilizador SQL e a palavra-passe. 
        6. Nome para o SQL data warehouse
        7. Nome da conta de armazenamento. 
        8. Nome da aplicação de função. 
    3.  Prima **ENTER** na janela do Cloud Shell para executar o comando. Este processo poderá demorar algum tempo, como está criando uma série de recursos. No resultado do comando, certifique-se de que tenham sido sem falhas. 

### <a name="close-the-cloud-shell"></a>Fechar o Cloud Shell 
Fechar o cloud shell, selecionando o **Cloud Shell** botão no portal (ou) **X** botão no canto superior direito da janela do Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Certifique-se de que os recursos são criados

1. No portal do Azure, selecione **grupos de recursos** no menu da esquerda. 
2. Filtre a lista de grupos de recursos ao introduzir o nome do grupo de recursos na caixa de pesquisa. 
3. Na lista, selecione o grupo de recursos.

    ![Selecione o grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confirme que vê os seguintes recursos no grupo de recursos:

    ![Recursos no grupo de recursos](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse
Criar uma tabela no seu armazém de dados ao executar o [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) script. Para executar o script, pode utilizar o Visual Studio ou o Editor de consultas no portal. Os passos seguintes mostram-lhe como utilizar o Editor de consultas: 

1. Na lista de recursos no grupo de recursos, selecione o SQL data warehouse. 
2. Na página de armazém de dados SQL, selecione **editor de consultas (pré-visualização)** no menu à esquerda. 

    ![Página de armazém de dados SQL](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Introduza o nome da **usuário** e **palavra-passe** para o SQL server e selecione **OK**. 

    ![Autenticação do SQL server](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Na janela da consulta, copie e execute o seguinte script SQL: 

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

    ![Executar a consulta SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Mantenha este separador ou janela aberta para que pode verificar que os dados são criados no final do tutorial. 


## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de Funções do Azure

1. Inicie o Visual Studio 2017. 
2. Abra o **EventHubsCaptureEventGridDemo.sln** solução que transferiu a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos.
3. No Explorador de Soluções, clique com o botão direito do rato em **FunctionEGDWDumper** e selecione **Publicar**.

   ![Publicar aplicação de funções](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se vir o ecrã seguinte, selecione **iniciar**. 

   ![Botão publicar de início](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na **escolher um destino da publicação** página, selecione a **selecionar existente** opção e selecione **criar perfil**. 

   ![Escolher um destino de publicação](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Na página do serviço de aplicações, selecione seu **subscrição do Azure**, selecione a **aplicação de funções** no seu grupo de recursos e selecione **OK**. 

   ![Página do serviço de aplicações](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecione publicar](media/event-grid-event-hubs-integration/select-publish.png)

Depois de publicar a função, está pronto para subscrever o evento.

## <a name="subscribe-to-the-event"></a>Subscrever o evento

1. Num novo separador ou nova janela de um navegador da web, navegue para o [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **grupos de recursos** no menu da esquerda. 
3. Filtre a lista de grupos de recursos ao introduzir o nome do grupo de recursos na caixa de pesquisa. 
4. Na lista, selecione o grupo de recursos.

    ![Selecione o grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Na lista, selecione o plano do serviço de aplicações. 
5. Na página do plano do serviço de aplicações, selecione **aplicações** no menu à esquerda e selecione a aplicação de funções. 

    ![Selecione a sua aplicação de funções](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expanda a aplicação de função, expanda as funções e, em seguida, selecione a sua função. 

    ![Selecionar a função do Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Selecione **subscrição do Event Grid adicionar** na barra de ferramentas. 
8. Na **criar subscrição do Event Grid** página, efetue as seguintes ações: 
    1. Na **detalhes de TÓPICO** secção, efetue as seguintes ações:
        1. Selecione a sua subscrição do Azure.
        2. Selecione o grupo de recursos do Azure.
        3. Selecione o espaço de nomes de Hubs de eventos.
    2. Na **detalhes da subscrição de evento** página, introduza um nome para a subscrição (por exemplo: captureEventSub) e selecione **criar**. 

        ![Criar subscrição do Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados
Acabou de configurar o seu hub de eventos, o armazém de dados SQL, aplicação de funções do Azure e a subscrição do evento. Antes de executar uma aplicação que gera dados para o hub de eventos, tem de configurar alguns valores.

1. No portal do Azure, navegue para o grupo de recursos como fez anteriormente. 
2. Selecione o espaço de nomes de Hubs de eventos.
3. Na **espaço de nomes de Hubs de eventos** página, selecione **políticas de acesso partilhado** no menu da esquerda.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 
5. Selecione o botão Copiar junto a **ligação chave primária da cadeia de caracteres** caixa de texto. 

    ![Cadeia de ligação para o espaço de nomes do hub de eventos](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Volte à sua solução do Visual Studio. 
2. No projeto WindTurbineDataGenerator, abra **program.cs**.
5. Substitua os dois valores constantes. Utilize o valor copiado para **EventHubConnectionString**. Utilize **hubdatamigration**, o nome do hub de eventos. Se utilizou um nome diferente para o hub de eventos, especifique esse nome. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilar a solução. Executar o **WindTurbineGenerator.exe** aplicação. 
7. Após alguns minutos, consulte a tabela no seu armazém de dados para os dados migrados.

    ![Resultados da consulta](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dados de eventos gerados pelo hub de eventos
O Event Grid distribui dados de eventos para os subscritores. O exemplo seguinte mostra os dados de eventos gerados quando os dados de transmissão em fluxo através de um hub de eventos são capturados num blob. Em particular, tenha em atenção a `fileUrl` propriedade no `data` objeto aponta para o blob no armazenamento. A aplicação de função utiliza este URL para recuperar o arquivo de Blobs com dados capturados.

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


## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre as diferenças dos serviços de mensagens do Azure, veja [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para obter uma introdução à Recolha dos Hubs de Eventos, veja [Ativar Recolha dos Hubs de Eventos com o portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, veja [Exemplo do Event Grid e da Recolha dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
