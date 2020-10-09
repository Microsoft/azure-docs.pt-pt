---
title: 'Tutorial: Enviar dados do Event Hubs para o armazém de dados - Grade de Eventos'
description: 'Tutorial: Descreve como usar a Azure Event Grid e os Centros de Eventos para migrar dados para um Azure Synapse Analytics. Utiliza uma Função Azure para recuperar um ficheiro Captura.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 64d4b9769e1a228294bd7d8741f6f4b1260fb0dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91270564"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir big data para um armazém de dados
A Azure [Event Grid](overview.md) é um serviço inteligente de encaminhamento de eventos que lhe permite reagir a notificações (eventos) de apps e serviços. Por exemplo, pode desencadear uma Função Azure para processar dados do Event Hubs que foram capturados para um armazenamento Azure Blob ou Azure Data Lake Storage, e migrar os dados para outros repositórios de dados. Esta [amostra de integração de Centros de Eventos e Grelha de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra-lhe como usar os Centros de Eventos com a Grade de Eventos para migrar sem problemas dados de Centros de Eventos capturados, desde o armazenamento de blob até um Azure Synapse Analytics (anteriormente SQL Data Warehouse).

![Descrição geral da aplicação](media/event-grid-event-hubs-integration/overview.png)

Este diagrama retrata o fluxo de trabalho da solução que se constrói neste tutorial: 

1. Os dados enviados para um centro de eventos Azure são capturados num armazenamento de bolhas Azure.
2. Quando a captura de dados está completa, um evento é gerado e enviado para uma grelha de eventos Azure. 
3. A grelha de eventos encaminha estes dados do evento para uma aplicação de função Azure.
4. A aplicação de função utiliza o URL blob nos dados do evento para recuperar a bolha do armazenamento. 
5. A aplicação de função migra os dados blob para um Azure Synapse Analytics. 

Neste artigo, tome os seguintes passos:

> [!div class="checklist"]
> * Utilize um modelo de Gestor de Recursos Azure para implantar a infraestrutura: um centro de eventos, uma conta de armazenamento, uma aplicação de função, um Synapse Analytics.
> * Crie uma tabela no armazém de dados.
> * Adicione código à aplicação de função.
> * Subscreva o evento. 
> * Executar aplicativo que envia dados para o centro de eventos.
> * Ver dados migrados no armazém de dados.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, tem de ter:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Estúdio visual 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de desktop .NET, desenvolvimento de Azure, ASP.NET e desenvolvimento web, desenvolvimento Node.js e desenvolvimento python.
* Descarregue o projeto de [amostra EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para o seu computador.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Neste passo, você implanta a infraestrutura necessária com um [modelo de Gestor de Recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando implementa o modelo, são criados os seguintes recursos:

* Centro de eventos com a funcionalidade Captura ativada.
* Conta de armazenamento dos ficheiros capturados. 
* Plano de serviço de aplicações para hospedar a app de função
* Aplicação de funções para processar o evento
* SQL Server para alojar o armazém de dados
* Azure Synapse Analytics para armazenar os dados migrados

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Lançar Azure Cloud Shell no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione o botão **Cloud Shell** na parte superior.

    ![Portal do Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Você vê a Cloud Shell aberta na parte inferior do navegador.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Na Cloud Shell, se vir uma opção para selecionar entre **Bash** e **PowerShell,** selecione **Bash**. 
5. Se estiver a utilizar o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **Criar armazenamento**. A Azure Cloud Shell requer uma conta de armazenamento Azure para armazenar alguns ficheiros. 

    ![Screenshot que mostra o diálogo "Não tem armazenamento montado" com o botão "Criar armazenamento" selecionado.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Espere até que a Cloud Shell seja inicializada. 

    ![Criar armazenamento para casca de nuvem](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Utilizar a CLI do Azure

1. Criar um grupo de recursos Azure executando o seguinte comando CLI: 
    1. Copie e cole o seguinte comando na janela Cloud Shell. Altere o nome e a localização do grupo de recursos, se quiser.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Pressione **ENTER**. 

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
2. Implementar todos os recursos mencionados na secção anterior (centro de eventos, conta de armazenamento, app de funções, Azure Synapse Analytics) executando o seguinte comando CLI: 
    1. Copie e cole o comando na janela Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copiar o comando para a Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Especificar valores para as seguintes entidades:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nome do centro do evento. 
        3. Nome para o centro de eventos. Pode deixar o valor tal como está (hubdatamigration).
        4. Nome para o servidor SQL.
        5. Nome do utilizador e palavra-passe SQL. 
        6. Nome para a Azure Synapse Analytics
        7. Nome da conta de armazenamento. 
        8. Nome para a aplicação de função. 
    3.  Prima **ENTER** na janela Cloud Shell para executar o comando. Este processo pode demorar algum tempo, uma vez que está a criar um monte de recursos. Em resultado do comando, certifique-se de que não houve falhas. 
    

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. No Azure Cloud Shell, mude para o modo PowerShell. Selecione seta no canto superior esquerdo da Azure Cloud Shell e selecione **PowerShell**.

    ![Mudar para PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Criar um grupo de recursos Azure executando o seguinte comando: 
    1. Copie e cole o seguinte comando na janela Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Especificar um nome para o **grupo de recursos.**
    3. Prima ENTER. 
3. Implementar todos os recursos mencionados na secção anterior (centro de eventos, conta de armazenamento, app de funções, Azure Synapse Analytics) executando o seguinte comando:
    1. Copie e cole o comando na janela Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copiar o comando para a Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Especificar valores para as seguintes entidades:
        1. Nome do grupo de recursos que criou anteriormente.
        2. Nome para o espaço de nome do centro do evento. 
        3. Nome para o centro de eventos. Pode deixar o valor tal como está (hubdatamigration).
        4. Nome para o servidor SQL.
        5. Nome do utilizador e palavra-passe SQL. 
        6. Nome para a Azure Synapse Analytics
        7. Nome da conta de armazenamento. 
        8. Nome para a aplicação de função. 
    3.  Prima **ENTER** na janela Cloud Shell para executar o comando. Este processo pode demorar algum tempo, uma vez que está a criar um monte de recursos. Em resultado do comando, certifique-se de que não houve falhas. 

### <a name="close-the-cloud-shell"></a>Feche a Casca de Nuvem 
Feche a casca de nuvem selecionando o botão **Cloud Shell** no botão portal (ou) **X** no canto superior direito da janela Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verifique se os recursos são criados

1. No portal Azure, selecione **grupos de recursos** no menu esquerdo. 
2. Filtrar a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione o seu grupo de recursos na lista.

    ![Selecione o seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confirme que vê os seguintes recursos no grupo de recursos:

    ![Recursos no grupo de recursos](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Criar uma tabela no Azure Synapse Analytics
Crie uma tabela no seu armazém de dados executando o script [CreateDataWarehouseTable.sql.](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) Para executar o script, pode utilizar o Visual Studio ou o Editor de Consulta no portal. Os seguintes passos mostram-lhe como usar o Editor de Consulta: 

1. Na lista de recursos do grupo de recursos, selecione o seu **pool Synapse SQL (data warehouse)**. 
2. Na página Azure Synapse Analytics, selecione **Editor de Consulta (pré-visualização)** no menu esquerdo. 

    ![Página Azure Synapse Analytics](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Introduza o nome do **utilizador** e **da palavra-passe** para o servidor SQL e selecione **OK**. Poderá ser necessário adicionar o endereço IP do seu cliente à firewall para iniciar sessão com sucesso no servidor SQL. 

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

### <a name="update-the-function-runtime-version"></a>Atualizar a versão de tempo de execução da função

1. No portal Azure, selecione **grupos de recursos** no menu esquerdo.
2. Selecione o grupo de recursos em que existe a aplicação de função. 
3. Selecione a aplicação de função do Serviço de **Aplicações** tipo na lista de recursos do grupo de recursos.
4. Selecione **Configuração** em **Definições** no menu esquerdo. 
5. Mude para o separador **de definição de tempo de funcionamento** da função no painel direito. 
5. Atualize a **versão de tempo de execução** para **~3**. 

    ![Vertar versão de tempo de execução da função](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de Funções do Azure

1. Lançamento Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo.sln** que descarregou do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos.
3. No Explorador de Soluções, clique com o botão direito do rato em **FunctionEGDWDumper** e selecione **Publicar**.

   ![Publicar aplicação de funções](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se vir o ecrã a seguir, selecione **Iniciar**. 

   ![Screenshot que mostra Visual Studios com o botão "Iniciar" na secção Publicar.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na caixa de diálogo **publicar,** selecione **Azure** for **Target**e selecione **Seguinte**. 

   ![Começar a publicar botão](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Selecione **Azure Function App (Windows)** e selecione **Next**. 

   ![Selecione App de Função Azure - Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. No separador **De instância funções,** selecione a subscrição do Azure, expanda o grupo de recursos e selecione a aplicação de função e, em seguida, selecione **Terminar**. Tem de assinar na sua conta Azure se ainda não o fez. 

   ![Selecione a sua aplicação de função](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. Na secção **Dependências de Serviço,** selecione **Configure**.
9. Na página **de dependência do Configure,** selecione a conta de armazenamento que criou anteriormente e, em seguida, selecione **Seguinte**. 
10. Mantenha as definições para o nome e valor da cadeia de ligação e selecione **Next**.
11. Limpe a opção **secrets store** e, em seguida, selecione **Termine**.  
8. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecione publicar](media/event-grid-event-hubs-integration/select-publish.png)

Depois de publicar a função, está pronto para subscrever o evento.

## <a name="subscribe-to-the-event"></a>Subscrever o evento

1. Num novo separador ou nova janela de um navegador web, navegue para o [portal Azure](https://portal.azure.com).
2. No portal Azure, selecione **grupos de recursos** no menu esquerdo. 
3. Filtrar a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione o seu grupo de recursos na lista.

    ![Selecione o seu grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selecione o plano de Serviço de Aplicações (não o Serviço de Aplicações) na lista de recursos do grupo de recursos. 
5. Na página do Plano de Serviço de Aplicações, selecione **Apps** no menu esquerdo e selecione a aplicação de função. 

    ![Selecione o seu aplicativo de funções](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expanda a aplicação de funções, expanda as funções e, em seguida, selecione a sua função. 
7. Selecione **Adicionar a subscrição da Grelha de Eventos** na barra de ferramentas. 

    ![Selecionar a função do Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Na página **de subscrição da Grelha de Eventos,** faça as seguintes ações: 
    1. Na página DETALHES DA SUBSCRIÇÃO DO **EVENTO,** introduza um nome para a subscrição (por exemplo: captureEventSub) e selecione **Criar**. 
    2. Na secção **DETALHES TÓPICOS,** faça as seguintes ações:
        1. Selecione **os espaços de nomes de centros de eventos** para **tipos de tópicos.** 
        2. Selecione a sua subscrição do Azure.
        2. Selecione o grupo de recursos Azure.
        3. Selecione o seu espaço de nomes 'Event Hubs'.
    3. Na secção **TIPOS DE EVENTO,** confirme que o **Ficheiro captura criado** está selecionado para **filtrar para tipos de eventos.** 
    4. Na secção **ENDPOINT DETAILS,** confirme que o **tipo endpoint** está definido para **Função Azure** e **o Ponto Final** está definido para a função Azure. 
    
        ![Criar subscrição de Grade de Eventos](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados
Terminou a configuração do seu centro de eventos, Azure Synapse Analytics, app de função Azure e subscrição de eventos. Antes de executar uma aplicação que gera dados para o hub de eventos, tem de configurar alguns valores.

1. No portal Azure, navegue para o seu grupo de recursos como fez anteriormente. 
2. Selecione o espaço de nomes 'Centros de Eventos'.
3. Na página **De Nomes do Event Hubs,** selecione **políticas de acesso partilhado** no menu esquerdo.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 
5. Selecione o botão de cópia ao lado da caixa de texto **de tecla primária de ligação.** 

    ![Cadeia de conexão para o espaço de nome do centro de eventos](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Volte para a sua solução visual Studio. 
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
O Event Grid distribui dados de eventos para os subscritores. O exemplo a seguir mostra dados de eventos gerados quando os dados que são transmitidos através de um centro de eventos são capturados numa bolha. Em particular, note que a `fileUrl` propriedade no objeto aponta para a bolha no `data` armazenamento. A aplicação de função utiliza este URL para recuperar o ficheiro blob com dados capturados.

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
