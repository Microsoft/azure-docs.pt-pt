---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854267"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Visão geral da aplicação":::

Este diagrama retrata o fluxo de trabalho da solução que se constrói neste tutorial: 

1. Os dados enviados para um centro de eventos Azure são capturados num armazenamento de bolhas Azure.
2. Quando a captura de dados está completa, um evento é gerado e enviado para uma grelha de eventos Azure. 
3. A grelha de eventos encaminha estes dados do evento para uma aplicação de função Azure.
4. A aplicação de função utiliza o URL blob nos dados do evento para recuperar a bolha do armazenamento. 
5. A aplicação de função migra os dados blob para um Azure Synapse Analytics. 

Neste artigo, tome os seguintes passos:

> [!div class="checklist"]
> - Implementar a infraestrutura necessária para o tutorial
> - Publicar o código para uma Aplicação de Funções
> - Criar uma subscrição do Event Grid 
> - Transmita os dados da amostra para os centros de eventos
> - Verifique os dados capturados no Azure Synapse Analytics

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, tem de ter:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Estúdio visual 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de desktop .NET, desenvolvimento de Azure, ASP.NET e desenvolvimento web, desenvolvimento Node.js e desenvolvimento python.
- Descarregue o projeto de [amostra EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para o seu computador.
    - WindTurbineDataGenerator – Um simples editor que envia dados de turbinas eólicas de amostra para um centro de eventos habilitado para captura
    - FunctionDWDumper – uma Função do Azure que recebe uma notificação do Event Grid quando um ficheiro Avro é capturado para o blob de Armazenamento do Azure. Recebe o caminho URI da blob, lê o seu conteúdo e empurra estes dados para a Azure Synapse Analytics (pool de SQL dedicado).

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Neste passo, você implanta a infraestrutura necessária com um [modelo de Gestor de Recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando implementa o modelo, são criados os seguintes recursos:

* Centro de eventos com a funcionalidade Captura ativada.
* Conta de armazenamento dos ficheiros capturados. 
* Plano de serviço de aplicações para hospedar a app de função
* Aplicação de funções para processar o evento
* SQL Server para alojar o armazém de dados
* Azure Synapse Analytics (piscina SQL dedicada) para armazenar os dados migrados

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Utilize o Azure CLI para implantar a infraestrutura

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione o botão **Cloud Shell** na parte superior.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Portal do Azure":::
3. Você vê a Cloud Shell aberta na parte inferior do navegador.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Na Cloud Shell, se vir uma opção para selecionar entre **Bash** e **PowerShell,** selecione **Bash**. 
5. Se estiver a utilizar o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **Criar armazenamento**. A Azure Cloud Shell requer uma conta de armazenamento Azure para armazenar alguns ficheiros. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Criar armazenamento para Cloud Shell":::
6. Espere até que a Cloud Shell seja inicializada. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inicializada":::
1. Criar um grupo de recursos Azure executando o seguinte comando CLI: 
    1. Copie e cole o seguinte comando na janela Cloud Shell. Altere o nome e a localização do grupo de recursos, se quiser.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Pressione **ENTER**. 

        Segue-se um exemplo:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implementar todos os recursos mencionados na secção anterior (centro de eventos, conta de armazenamento, app de funções, Azure Synapse Analytics) executando o seguinte comando CLI: 
    1. Copie e cole o comando na janela Cloud Shell. Em alternativa, pode querer copiar/colar num editor à sua escolha, definir valores e, em seguida, copiar o comando para a Cloud Shell. 

        > [!IMPORTANT]
        > Especificar os valores para as seguintes entidades antes de executar o comando: 
        > - Nome do grupo de recursos que criou anteriormente.
        > - Nome para o espaço de nome do centro do evento. 
        > - Nome para o centro de eventos. Pode deixar o valor tal como está (hubdatamigration).
        > - Nome para o servidor SQL.
        > - Nome do utilizador e palavra-passe SQL. 
        > - Nome da base de dados.
        > - Nome da conta de armazenamento. 
        > - Nome para a aplicação de função. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Prima **ENTER** na janela Cloud Shell para executar o comando. Este processo pode demorar algum tempo, uma vez que está a criar um monte de recursos. Em resultado do comando, certifique-se de que não houve falhas. 
1. Feche o Cloud Shell selecionando o botão **Cloud Shell** no botão portal (ou) **X** no canto superior direito da janela Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verifique se os recursos são criados

1. No portal Azure, selecione **grupos de recursos** no menu esquerdo. 
2. Filtrar a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione o seu grupo de recursos na lista.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Selecione o seu grupo de recursos":::
4. Confirme que vê os seguintes recursos no grupo de recursos:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Recursos no grupo de recursos" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Criar uma tabela no Azure Synapse Analytics
Crie uma tabela no seu armazém de dados executando o script [.sql CreateDataWarehouseTable.](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) Para executar o script, pode utilizar o Visual Studio ou o Editor de Consulta no portal. Os seguintes passos mostram-lhe como usar o Editor de Consulta: 

1. Na lista de recursos do grupo de recursos, selecione a sua **piscina SQL dedicada.** 
2. Na página **de piscina dedicada SQL,** na secção **Tarefas Comuns** no menu esquerdo, selecione **o editor de consulta (pré-visualização)**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Página Azure Synapse Analytics":::
2. Introduza o nome do **utilizador** e **da palavra-passe** para o servidor SQL e selecione **OK**. Se vir uma mensagem sobre permitir que o seu cliente aceda ao servidor SQL, siga estes passos:
    1. Selecione o link: **Definir firewall do servidor**. 
    2. Na página de **definições de Firewall,** selecione **Adicionar IP** ao cliente na barra de ferramentas e, em seguida, selecione **Guardar** na barra de ferramentas. 
    3. Selecione **OK** na mensagem de sucesso.
    4. Volte para a página **de piscina dedicada SQL** e selecione **o editor de consulta (pré-visualização)** no menu esquerdo. 
    5. Introduza **o utilizador** e **a palavra-passe** e, em seguida, selecione **OK**. 
1. Na janela de consulta, copie e execute o seguinte script SQL: 

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

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Executar consulta SQL":::
5. Mantenha este separador ou janela aberto para que possa verificar se os dados são criados no final do tutorial. 

### <a name="update-the-function-runtime-version"></a>Atualizar a versão de tempo de execução da função

1. Abra outro separador no navegador web e navegue para o [portal Azure.](https://portal.azure.com)
1. No portal Azure, selecione **grupos de recursos** no menu esquerdo.
1. Selecione o grupo de recursos em que existe a aplicação de função. 
1. Selecione a **aplicação de função** na lista de recursos do grupo de recursos.
1. Selecione **Configuração** em **Definições** no menu esquerdo. 
1. Mude para o separador **de definição de tempo de funcionamento** da função no painel direito. 
1. Atualize a **versão de tempo de execução** para **~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Vertar versão de tempo de execução da função":::
6. Selecione **Guardar** na barra de ferramentas. 
1. No **Save altera** o pop-up de confirmação, selecione **Continue**. 

## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de Funções do Azure

1. Lançamento Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo.sln** que descarregou a partir do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos. Pode encontrá-lo na `/samples/e2e/EventHubsCaptureEventGridDemo` pasta. 
3. No Solution Explorer, clique à direita no projeto **FunctionEGDWDumper** e **selecione Publicar**.
4. Se vir o ecrã a seguir, selecione **Iniciar**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Botão de arranque na secção Publicar.":::
5. Na caixa de diálogo **publicar,** selecione **Azure** for **Target** e selecione **Seguinte**. 
6. Selecione **Azure Function App (Windows)** e selecione **Next**.
7. No separador **De instância funções,** selecione a subscrição do Azure, expanda o grupo de recursos e selecione a aplicação de função e, em seguida, selecione **Terminar**. Tem de assinar na sua conta Azure se ainda não o fez. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Selecione a sua aplicação de função":::
8. Na página **Publicar,** na secção Dependências de **Serviço,** selecione **Configurar** para **Armazenamento.** 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Selecione link de configuração para a dependência do serviço de armazenamento":::
1. Na página de dependência do **Configure,** siga estes passos: 
    1. selecione a **conta de armazenamento** que criou anteriormente e, em seguida, selecione **Next**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Selecione conta de armazenamento":::
    10. Especifique um **nome para a cadeia de ligação** e selecione **Nenhum** para a opção de cadeia **de ligação Save** e, em seguida, selecione **Seguinte**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Especifique o nome do string de ligação":::      
    1. Limpe a opção **de código C#** e a loja **Secrets** e, em seguida, selecione **Terminar**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Resumo de revisão das alterações":::
1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Selecione publicar":::
2. No separador que tem a página **Azure Function** aberta, selecione  **Funções** no menu esquerdo. Confirme se a função **EventGridTriggerMigrateData** aparece na lista. Se não o vir, tente publicar no Visual Studio novamente e, em seguida, refresque a página no portal. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Confirmar criação de função":::    

Depois de publicar a função, está pronto para subscrever o evento.

## <a name="subscribe-to-the-event"></a>Subscrever o evento

1. Num novo separador ou nova janela de um navegador web, navegue para o [portal Azure](https://portal.azure.com).
2. No portal Azure, selecione **grupos de recursos** no menu esquerdo. 
3. Filtrar a lista de grupos de recursos inserindo o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione o seu grupo de recursos na lista.
1. Selecione o **espaço de nomes do Event Hubs** na lista de recursos.
1. Na página **'Event Hubs Namespace',** selecione **Eventos** no menu esquerdo e, em seguida, selecione **+ Subscrição de evento** na barra de ferramentas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Adicione link de subscrição de eventos na página Eventos para um espaço de nomes de Event Hubs":::
1. Na página **De Subscrição de Eventos** Criar, siga estes passos:
    1. Insira um nome para a **subscrição** do evento . 
    1. Insira um nome para o tópico do **sistema**. Um tópico do sistema fornece um ponto final para o remetente enviar eventos. Para mais informações, consulte [tópicos do Sistema](../articles/event-grid/system-topics.md)
    1. Para **o tipo de ponto final**, selecione **Azure Function**.
    1. Para **Endpoint,** selecione o link.
    1. Na página **'Selecione Azure Function',** siga estes passos se não estiverem preenchidos automaticamente.
        1. Selecione a subscrição Azure que tem a função Azure. 
        1. Selecione o grupo de recursos para a função. 
        1. Selecione a aplicação de função.
        1. Selecione a ranhura de implantação. 
        1. Selecione a função **EventGridTriggerMigrateData**. 
    1. Na página **'Selecione', selecione** **'Selecção' 'Confirmar'.**
    1. Em seguida, de volta à página **de Subscrição** de Eventos, selecione **Criar**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Criar uma subscrição de eventos usando a função" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Verifique se a subscrição do evento foi criada. Mude para o separador **Subscrições de Eventos** na página **Eventos** para o espaço de nomes do Event Hubs. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Confirmar subscrição de evento" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Selecione o plano de Serviço de Aplicações (não o Serviço de Aplicações) na lista de recursos do grupo de recursos. 

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados
Terminou a criação do seu centro de eventos, dedicou pool SQL (anteriormente SQL Data Warehouse), app de função Azure e subscrição de eventos. Antes de executar uma aplicação que gera dados para o hub de eventos, tem de configurar alguns valores.

1. No portal Azure, navegue para o seu grupo de recursos como fez anteriormente. 
2. Selecione o espaço de nomes 'Centros de Eventos'.
3. Na página **De Nomes do Event Hubs,** selecione **políticas de acesso partilhado** no menu esquerdo.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Página de políticas de acesso compartilhado para um espaço de nomes de Centros de Eventos":::    
1. Selecione o botão de cópia ao lado da caixa de texto **de tecla primária de ligação.** 
1. Volte para a sua solução visual Studio. 
1. Clique à direita no projeto **WindTurbineDataGenerator** e selecione **set as Startup project**. 
1. No projeto WindTurbineDataGenerator, abra **program.cs**.
1. `<EVENT HUBS NAMESPACE CONNECTION STRING>`Substitua-a pela cadeia de ligação que copiou do portal. 
1. `<EVENT HUB NAME>`Substitua-o pelo nome do centro de eventos. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Compilar a solução. Executar a aplicação **WindTurbineGenerator.exe.** 
7. Após alguns minutos, no outro separador do navegador onde tem a janela de consulta aberta, consultar a tabela no seu armazém de dados para os dados migrados.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Resultados da consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Monitorizar a solução
Esta secção ajuda-o a monitorizar ou a resolver problemas na solução. 

### <a name="view-captured-data-in-the-storage-account"></a>Ver dados capturados na conta de armazenamento
1. Navegue para o grupo de recursos e selecione a conta de armazenamento utilizada para capturar dados do evento. 
1. Na página da **conta de Armazenamento,** selecione **Storage Explorer (pré-visualização)** no menu esquerdo.
1. Expandir **os recipientes BLOB** e selecionar **a sutura de vento**. 
1. Abra a pasta com o mesmo nome que o **seu espaço de nomes Desempaco** do Event Hubs no painel direito. 
1. Abra a pasta com o mesmo nome do seu centro de **eventos (hubdatamigration**). 
1. Perfure as pastas e veja os ficheiros AVRO. Eis um exemplo:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Ficheiro capturado no armazenamento" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Verifique se o gatilho da Grelha de Eventos invocou a função
1. Navegue para o grupo de recursos e selecione a aplicação de função. 
1. Selecione **Funções** no menu esquerdo.
1. Selecione a função **EventGridTriggerMigrateData** da lista. 
1. Na página **'Função',** selecione **Monitor** no menu esquerdo. 
1. Selecione **Configurar** para configurar insights de aplicação para capturar registos de invocação. 
1. Crie um novo recurso **Application Insights** ou utilize um recurso existente. 
1. Volte para a página **monitor para** a função. 
1. Confirme que a aplicação do cliente (**WindTurbineDataGenerator)** que está a enviar os eventos ainda está em execução. Caso contrário, execute a aplicação. 
1. Aguarde alguns minutos (5 minutos ou mais) e selecione o botão **Refresh** para ver as invocações da função.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Invocações de funções":::
1. Selecione uma invocação para ver detalhes.

    O Event Grid distribui dados de eventos para os subscritores. O exemplo a seguir mostra dados de eventos gerados quando os dados que são transmitidos através de um centro de eventos são capturados numa bolha. Em particular, note que a `fileUrl` propriedade no objeto aponta para a bolha no `data` armazenamento. A aplicação de função utiliza este URL para recuperar o ficheiro blob com dados capturados.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Verifique se os dados são armazenados na piscina dedicada SQL
No separador do navegador onde tem a janela de consulta aberta, questione a tabela na sua piscina DE SQL dedicada para os dados migrados.

![Resultados da consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

