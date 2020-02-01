---
title: 'Tutorial: Migrar dados do evento para o Armazém de Dados SQL - Hubs de Eventos Azure'
description: 'Tutorial: Este tutorial mostra-lhe como capturar dados do seu centro de eventos para um armazém de dados SQL utilizando uma função Azure desencadeada por uma grelha de eventos.'
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 01/15/2020
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: a83d65e497688fa97fbb2bdb5a4a72c6d29d81ae
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905685"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Tutorial: Migrate capturou dados de Hubs de Eventos para um Armazém de Dados SQL usando a Grelha de Eventos e Funções Azure

A [Captura](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) de Hubs de Eventos é a forma mais simples de entregar dados transmitidos nos Hubs de Eventos para um armazenamento de Blobs do Azure ou o arquivo do Azure Data Lake. Pode, em seguida, processar e entregar os dados para outros destinos de armazenamento à sua escolha, como o SQL Data Warehouse ou o Cosmos DB. Neste tutorial, irá aprender a capturar dados do seu hub de eventos para um armazém de dados SQL, através de uma função do Azure acionada por uma [grelha de eventos](https://docs.microsoft.com/azure/event-grid/overview).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Em primeiro lugar, crie um hub de eventos com a funcionalidade **Capturar** ativada e configure um armazenamento de blobs do Azure como destino. Os dados gerados pelo WindTurbineGenerator são transmitidos para o hub de eventos e capturados automaticamente para o Armazenamento do Azure como ficheiros Avro. 
*   Em seguida, vai criar uma subscrição do Azure Event Grid com o espaço de nomes dos Hubs de Eventos como origem e o ponto final de Função do Azure como o seu destino.
*   Sempre que um novo ficheiro Avro for entregue no blob de Armazenamento do Azure pela funcionalidade de Captura dos Hubs de Eventos, o Event Grid notifica a Função do Azure com o URI de blob. A Função, em seguida, migra dados do blob para um armazém de dados do SQL.

Neste tutorial, irá realizar as seguintes ações: 

> [!div class="checklist"]
> * Implementar a infraestrutura
> * Publicar o código para uma Aplicação de Funções
> * Criar uma subscrição do Event Grid a partir da aplicação de Funções
> * Transmitir dados de exemplo para o Hub de Eventos. 
> * Verificar dados recolhidos no SQL Data Warehouse

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Estúdio visual 2019.](https://www.visualstudio.com/vs/) Ao instalar, certifique-se de que instala as cargas de trabalho seguintes: desenvolvimento de ambiente de trabalho .NET, desenvolvimento do Azure, desenvolvimento de ASP.NET e Web, desenvolvimento de Node.js, desenvolvimento de Python
- Descarregue a [amostra Git](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) A solução de amostra contém os seguintes componentes:
    - *WindTurbineDataGenerator* – um editor simples que envia dados de turbina eólica de exemplo para um hub de eventos com a Captura ativada
    - *FunctionDWDumper* – uma Função do Azure que recebe uma notificação do Event Grid quando um ficheiro Avro é capturado para o blob de Armazenamento do Azure. Ele recebe o caminho do URI do blob, lê os seus conteúdos e envia estes dados para um SQL Data Warehouse.

    Esta amostra utiliza o mais recente pacote Azure.Messaging.EventHubs. Pode encontrar aqui a amostra antiga que utiliza o [pacote](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)Microsoft.Azure.EventHubs . 

### <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura
Utilize o Azure PowerShell ou a CLI do Azure para implementar a infraestrutura necessária para este tutorial com este [modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Este modelo cria os seguintes recursos:

-   Hub de Eventos com a funcionalidade de Captura ativada
-   Conta de armazenamento para os dados de eventos capturados
-   Plano do serviço de aplicações do Azure para alojar a aplicação de Funções
-   Aplicação de Funções para processamento de ficheiros de eventos capturados
-   SQL Server para alojar o Armazém de Dados
-   SQL Data Warehouse para armazenar os dados migrados

As secções seguintes oferecem os comandos da CLI do Azure e do Azure PowerShell para implementar a infraestrutura exigida para o tutorial. Atualize os nomes dos seguintes objetos antes de executar os comandos: 

- Grupo de recursos do Azure 
- Região do grupo de recursos
- Espaço de nomes dos Event Hubs
- Hub de eventos
- Servidor SQL do Azure
- Utilizador SQL (e palavra-passe)
- Base de dados SQL do Azure
- Armazenamento do Azure 
- Aplicação de Funções do Azure

Estes scripts demoram algum tempo a criar todos os artefactos do Azure. Aguarde até que o script seja concluído antes de continuar. Se a implementação falhar por algum motivo, elimine o grupo de recursos, corrija o problema comunicado e volte a executar o comando. 

#### <a name="azure-cli"></a>CLI do Azure
Para implementar o modelo com a CLI do Azure, utilize os seguintes comandos:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Para implementar o modelo com o PowerShell, utilize os seguintes comandos:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse 
Crie uma tabela no seu armazém de dados SQL ao executar o script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) com o [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), o [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) ou o Editor de Consulta no portal. 

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

## <a name="publish-code-to-the-functions-app"></a>Publicar o código na Aplicação de Funções

1. Abra a solução *EventHubsCaptureEventGridDemo.sln* no Visual Studio 2019.

1. No Explorador de Soluções, clique com o botão direito do rato em *FunctionEGDWDumper* e selecione **Publicar**.

   ![Publicar aplicação de funções](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Selecione **Aplicação de Funções do Azure** e **Selecionar Existente**. Selecione **Publicar**.

   ![Aplicação de funções de destino](./media/store-captured-data-data-warehouse/pick-target.png)

1. Selecione a aplicação de funções que implementou através do modelo. Selecione **OK**.

   ![Selecionar aplicação de funções](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecione publicar](./media/store-captured-data-data-warehouse/select-publish.png)

Depois de publicar a função, está pronto para subscrever o evento de captura a partir dos Hubs de Eventos!


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Criar uma subscrição do Event Grid a partir da aplicação de Funções
 
1. Aceda ao [Portal do Azure](https://portal.azure.com/). Selecione o grupo de recursos e a aplicação de funções.

   ![Ver aplicação de funções](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Selecione a função.

   ![Selecionar função](./media/store-captured-data-data-warehouse/select-function.png)

1. Selecione **Adicionar subscrição do Event Grid**.

   ![Adicionar subscrição](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Conceda um nome à subscrição da grelha de eventos. Utilize **Espaços de Nomes de Hubs de Eventos** como o tipo de evento. Indique os valores para selecionar a instância do espaço de nomes dos Hubs de Eventos. Deixe o ponto final do subscritor como o valor indicado. Selecione **Criar**.

   ![Criar subscrição](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Gerar dados de exemplo  
Acabou de configurar o seu Hub de Eventos, o armazém de dados SQL, a Function App do Azure e a subscrição do Event Grid. Pode executar o WindTurbineDataGenerator.exe para gerar fluxos de dados para o Hub de Eventos depois de atualizar a cadeia de ligação e o nome do seu hub de eventos no código fonte. 

1. No portal, selecione o seu espaço de nomes do hub de eventos. Selecione **Cadeias de Ligação**.

   ![Selecione cadeias de ligação](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Selecione **RootManageSharedAccessKey**

   ![Selecionar chave](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Cópia **Cadeia de ligação - chave primária**

   ![Chave de cópia](./media/store-captured-data-data-warehouse/copy-key.png)

4. Volte ao seu projeto do Visual Studio. No projeto *WindTurbineDataGenerator*, abra *program.cs*.

5. Atualize os valores para **EventHubConnectionString** e **EventHubName** com a cadeia de ligação e o nome do seu hub de eventos. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compile a solução e, em seguida, execute a aplicação WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Verificar dados recolhidos no armazém de dados
Após alguns minutos, consulte a tabela no seu armazém de dados SQL. Observe que os dados gerados pelo WindTurbineDataGenerator foram transmitidos para o seu Hub de Eventos, capturados para um contentor de Armazenamento do Azure e, em seguida, migrados para a tabela do SQL Data Warehouse pela Função do Azure.  

## <a name="next-steps"></a>Passos seguintes 
Pode utilizar ferramentas de visualização de dados avançadas com o armazém de dados para obter informações acionáveis.

Este artigo mostra como utilizar o [Power BI com o SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi)



