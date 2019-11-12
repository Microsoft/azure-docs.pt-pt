---
title: Registo de diagnósticos no Azure Cosmos DB
description: Saiba mais sobre as diferentes maneiras de registrar e monitorar dados armazenados no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: bdbc50983708327cf5d3857282c92fcab1c28b09
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930536"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Registo de diagnósticos no Azure Cosmos DB 

Depois de começar a usar um ou mais bancos de dados do Azure Cosmos, talvez você queira monitorar como e quando seus bancos de dados são acessados. Este artigo fornece uma visão geral dos logs que estão disponíveis na plataforma Azure. Você aprende a habilitar o log de diagnóstico para fins de monitoramento para enviar logs para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/), como transmitir logs para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)e como exportar logs para [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Logs disponíveis no Azure

Antes de falarmos sobre como monitorar sua conta de Azure Cosmos DB, vamos esclarecer algumas coisas sobre registro em log e monitoramento. Há diferentes tipos de logs na plataforma Azure. Há [logs de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [logs de diagnóstico do](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)Azure, [métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventos, monitoramento de pulsação, logs de operações e assim por diante. Há uma infinidade de logs. Você pode ver a lista completa de logs nos [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) no portal do Azure. 

A imagem a seguir mostra os diferentes tipos de logs do Azure que estão disponíveis:

![Diferentes tipos de logs do Azure](./media/logging/azurelogging.png)

Na imagem, os **recursos de computação** representam os recursos do Azure para os quais você pode acessar o sistema operacional convidado da Microsoft. Por exemplo, máquinas virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, serviço de contêiner do Azure e assim por diante, são considerados recursos de computação. Os recursos de computação geram logs de atividade, logs de diagnóstico e logs de aplicativo. Para saber mais, consulte o artigo [fontes de monitoramento de dados no Azure](../azure-monitor/platform/data-sources.md) .

Os **recursos que não** são de computação são recursos nos quais você não pode acessar o sistema operacional subjacente e trabalhar diretamente com o recurso. Por exemplo, grupos de segurança de rede, aplicativos lógicos e assim por diante. Azure Cosmos DB é um recurso que não é de computação. Você pode exibir os logs para recursos que não são de computação no log de atividades ou habilitar a opção logs de diagnóstico no Portal. Para saber mais, consulte as [fontes de dados no artigo Azure monitor](../azure-monitor/platform/data-sources.md) .

O log de atividades registra as operações em um nível de assinatura para Azure Cosmos DB. Operações como ListKeys, Write DatabaseAccounts e mais são registradas em log. Os logs de diagnóstico fornecem log mais granular e permitem que você registre DataPlaneRequests (criar, ler, consultar e assim por diante) e MongoRequests.


Neste artigo, nos concentramos no log de atividades do Azure, nos logs de diagnóstico do Azure e nas métricas do Azure. Qual é a diferença entre esses três logs? 

### <a name="azure-activity-log"></a>Registo de Atividades do Azure

O log de atividades do Azure é um log de assinatura que fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. O log de atividades relata eventos de plano de controle para suas assinaturas na categoria administrativa. Você pode usar o log de atividades para determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) nos recursos em sua assinatura. Você também pode entender o status da operação e outras propriedades relevantes. 

O log de atividades difere dos logs de diagnóstico. O log de atividades fornece dados sobre as operações em um recurso de fora (o _plano de controle_). No contexto de Azure Cosmos DB, as operações de plano de controle incluem criar contêiner, chaves de lista, excluir chaves, banco de dados de lista e assim por diante. Os logs de diagnóstico são emitidos por um recurso e fornecem informações sobre a operação desse recurso (o _plano de dados_). Alguns exemplos das operações do plano de dados no log de diagnóstico são excluir, inserir e ReadFeed.

Os logs de atividade (operações do plano de controle) podem ser mais ricos por natureza e podem incluir o endereço de email completo do chamador, endereço IP do chamador, nome do recurso, nome da operação, Tenantid e muito mais. O log de atividades contém várias [categorias](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) de dados. Para obter detalhes completos sobre o Schemata dessas categorias, consulte [esquema de eventos do log de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). No entanto, os logs de diagnóstico podem ser restritivos por natureza, pois os dados pessoais geralmente são eliminados desses logs. Você pode ter o endereço IP do chamador, mas o último octant é removido.

### <a name="azure-metrics"></a>Métricas do Azure

As [métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) têm o tipo mais importante de dados de telemetria do Azure (também chamados de _contadores de desempenho_) emitidos pela maioria dos recursos do Azure. As métricas permitem exibir informações sobre a taxa de transferência, o armazenamento, a consistência, a disponibilidade e a latência de seus recursos de Azure Cosmos DB. Para obter mais informações, consulte [monitorando e Depurando com métricas em Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Logs de diagnóstico do Azure

Os logs de diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. Esses logs são capturados por solicitação. O conteúdo desses logs varia por tipo de recurso. Os logs de diagnóstico no nível de recurso também diferem dos logs de diagnóstico no nível do sistema operacional convidado. Os logs de diagnóstico do SO convidado são coletados por um agente em execução dentro de uma máquina virtual ou outro tipo de recurso com suporte. Os logs de diagnóstico no nível do recurso não exigem nenhum agente e capturam dados específicos do recurso da própria plataforma do Azure. Os logs de diagnóstico no nível do SO convidado capturam dados do sistema operacional e dos aplicativos que estão sendo executados em uma máquina virtual.

![Log de diagnóstico para armazenamento, hubs de eventos ou logs de Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>O que é registrado pelos logs de diagnóstico do Azure?

* Todas as solicitações de back-end autenticadas (TCP/REST) em todas as APIs são registradas, incluindo solicitações com falha como resultado de permissões de acesso, erros do sistema ou solicitações inadequadas. O suporte para solicitações de grafo, Cassandra e API de Tabela iniciadas pelo usuário não está disponível no momento.
* Operações no próprio banco de dados, que incluem operações CRUD em todos os documentos, contêineres e bancos de dados.
* Operações em chaves de conta, que incluem criar, modificar ou excluir as chaves.
* Pedidos não autenticados que resultam numa resposta 401. Por exemplo, solicitações que não têm um token de portador ou estão malformadas ou expiradas ou têm um token inválido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Ativar o registro em log no portal do Azure

Use as etapas a seguir para habilitar o log de diagnóstico no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

1. Navegue até sua conta do Azure Cosmos. Abra o painel **configurações de diagnóstico** e selecione a opção **Adicionar configuração de diagnóstico** .

    ![Ativar o log de diagnóstico para Azure Cosmos DB no portal do Azure](./media/logging/turn-on-portal-logging.png)

1. Na página **configurações de diagnóstico** , preencha o formulário com os seguintes detalhes: 

    * **Nome**: Insira um nome para os logs a serem criados.

    * Você pode armazenar os logs nos seguintes serviços:

      * **Arquivar em uma conta de armazenamento**: para usar essa opção, você precisa de uma conta de armazenamento existente para se conectar. Para criar uma nova conta de armazenamento no portal, consulte [o artigo criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) . Em seguida, retorne ao painel configurações de diagnóstico do Azure Cosmos DB no portal para selecionar sua conta de armazenamento. Pode levar alguns minutos para que contas de armazenamento criadas recentemente apareçam no menu suspenso.

      * **Transmitir para um hub de eventos**: para usar essa opção, você precisa de um namespace existente de hubs de eventos e Hub de eventos para se conectar. Para criar um namespace de hubs de eventos, consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o namespace do hub de eventos e o nome da política.

      * **Enviar para log Analytics**: para usar essa opção, use um espaço de trabalho existente ou crie um novo espaço de trabalho do log Analytics seguindo as etapas para [criar um novo espaço de trabalho](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) no Portal. 

   * Você pode registrar os seguintes dados:

      * **DataPlaneRequests**: Selecione esta opção para registrar solicitações de back-end para todas as APIs que incluem as contas SQL, Graph, MongoDB, Cassandra e API de Tabela no Azure Cosmos DB. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente após o período de retenção expirar. Os dados JSON a seguir são um exemplo de saída de detalhes registrados usando DataPlaneRequests. As propriedades de chave a serem observadas são: Requestcharge, statusCode, clientIPaddress e PartitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário do front-end para atender a solicitações para a API do Azure Cosmos DB para MongoDB. As solicitações do MongoDB serão exibidas em MongoRequests, bem como DataPlaneRequests. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente após o período de retenção expirar. Os dados JSON a seguir são um exemplo de saída de detalhes registrados usando MongoRequests. As propriedades de chave a serem observadas são: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Selecione esta opção para registrar o texto da consulta que foi executado.  Os dados JSON a seguir são um exemplo de saída de detalhes registrados usando QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: esse log relata as estatísticas das chaves de partição. Atualmente, as estatísticas são representadas com o tamanho do armazenamento (KB) das chaves de partição. O log é emitido em relação às três primeiras chaves de partição que ocupam a maior parte do armazenamento de dados.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Solicitações de métrica**: Selecione esta opção para armazenar dados detalhados em [métricas do Azure](../azure-monitor/platform/metrics-supported.md). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente após o período de retenção expirar.

3. Selecione **Guardar**.

    Se você receber um erro que diz "falha ao atualizar o diagnóstico para \<nome do espaço de trabalho >. A assinatura \<ID de assinatura > não está registrada para usar o Microsoft. insights, "siga as instruções de [diagnóstico do Azure de solução de problemas](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta e repita esse procedimento.

    Se você quiser alterar o modo como os logs de diagnóstico são salvos em qualquer ponto no futuro, retorne a esta página para modificar as configurações de log de diagnóstico para sua conta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Ativar o registro em log usando CLI do Azure

Para habilitar as métricas e o log de diagnóstico usando CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento de logs de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   O `resource` é o nome da conta de Azure Cosmos DB. O recurso está no formato "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/< Azure_Cosmos_account_name >" o `storage-account` é o nome da conta de armazenamento para a qual você deseja enviar os logs. Você pode registrar outros logs atualizando os valores de parâmetro de categoria para "MongoRequests" ou "DataPlaneRequests". 

- Para habilitar o streaming de logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   O `resource` é o nome da conta de Azure Cosmos DB. O `event-hub-rule` é a ID da regra do hub de eventos. 

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho Log Analytics, use este comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Você pode combinar esses parâmetros para habilitar várias opções de saída.

## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registro em log usando o PowerShell

Para ativar o log de diagnóstico usando o PowerShell, você precisa do Azure PowerShell com uma versão mínima do 1.0.1.

Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se você já tiver instalado Azure PowerShell e não souber a versão, no console do PowerShell, digite `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ligar às suas subscrições
Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Azure PowerShell obtém todas as assinaturas associadas a essa conta e, por padrão, usa a primeira.

Se você tiver mais de uma assinatura, talvez seja necessário especificar a assinatura específica que foi usada para criar o cofre de chaves do Azure. Para ver as assinaturas da sua conta, digite o seguinte comando:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada à conta de Azure Cosmos DB que você está registrando, digite o seguinte comando:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se você tiver mais de uma assinatura associada à sua conta, é importante especificar a assinatura que deseja usar.
>
>

Para obter mais informações sobre como configurar Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Criar uma nova conta de armazenamento para os seus registos
Embora você possa usar uma conta de armazenamento existente para seus logs, neste tutorial, criamos uma nova conta de armazenamento dedicada a Azure Cosmos DB logs. Para sua conveniência, armazenamos os detalhes da conta de armazenamento em uma variável chamada **SA**.

Para facilitar o gerenciamento adicional, neste tutorial, usamos o mesmo grupo de recursos que contém o banco de dados Cosmos do Azure. Substitua os valores dos parâmetros **ContosoResourceGroup**, **contosocosmosdblogs**e **North EUA Central** , conforme aplicável:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, a conta deverá usar a mesma assinatura que sua assinatura do Azure Cosmos DB. A conta também deve usar o modelo de implantação do Gerenciador de recursos, em vez do modelo de implantação clássico.
>
>

### <a id="identify"></a>Identificar a conta de Azure Cosmos DB para seus logs
Defina o nome da conta de Azure Cosmos DB como uma variável chamada **Account**, em que **resourceName** é o nome da conta de Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Ativar registo
Para habilitar o log para Azure Cosmos DB, use o cmdlet `Set-AzDiagnosticSetting` com variáveis para a nova conta de armazenamento, Azure Cosmos DB conta e a categoria a ser habilitada para registro em log. Execute o comando a seguir e defina o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

A saída do comando deve ser semelhante ao seguinte exemplo:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

A saída do comando confirma que o log agora está habilitado para seu banco de dados e as informações estão sendo salvas em sua conta de armazenamento.

Opcionalmente, você também pode definir a política de retenção para seus logs, de modo que os logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção com o sinalizador **-RetentionEnabled** definido como **$true**. Defina o parâmetro **-RetentionInDays** como **90** para que os logs com mais de 90 dias sejam excluídos automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Aceder aos seus registos
Os logs de Azure Cosmos DB para a categoria **DataPlaneRequests** são armazenados no contêiner **insights-logs-DataPlaneRequests** na conta de armazenamento que você forneceu. 

Primeiro, crie uma variável para o nome do contentor. A variável é usada em todo o passo a passo.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para listar todos os BLOBs neste contêiner, digite:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

A saída do comando deve ser semelhante ao seguinte exemplo:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Como você pode ver nessa saída, os BLOBs seguem uma Convenção de nomenclatura: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Como a mesma conta de armazenamento pode ser usada para coletar logs de vários recursos, você pode usar a ID de recurso totalmente qualificada no nome do blob para acessar e baixar os BLOBs específicos necessários. Antes de fazermos isso, abordamos como baixar todos os BLOBs.

Primeiro, crie uma pasta para transferir os blobs. Por exemplo:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os BLOBs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirecione essa lista por meio do comando `Get-AzStorageBlobContent` para baixar os BLOBs na pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando você executa esse segundo comando, o delimitador de **/** nos nomes de blob cria uma estrutura de pasta completa na pasta de destino. Essa estrutura de pastas é usada para baixar e armazenar os BLOBs como arquivos.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se você tiver vários bancos de dados e quiser baixar logs para apenas um banco de dados chamado **CONTOSOCOSMOSDB3**, use o comando:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se você tiver vários grupos de recursos e quiser baixar logs para apenas um grupo de recursos, use o comando `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Se você quiser baixar todos os logs do mês de julho de 2017, use o comando `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Você também pode executar os seguintes comandos:

* Para consultar o status das configurações de diagnóstico do recurso de banco de dados, use o comando `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Para desabilitar o registro em log da categoria **DataPlaneRequests** para o recurso de conta do banco de dados, use o comando `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Os blobs que são retornados em cada uma dessas consultas são armazenados como texto e formatados como um blob JSON, conforme mostrado no código a seguir:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Para saber mais sobre os dados em cada blob JSON, consulte [interpretar seus logs de Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs de diagnóstico são disponibilizados em sua conta por duas horas a partir da hora em que a operação de Azure Cosmos DB foi feita. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs e restringir quem pode acessá-los.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* O período de retenção para solicitações de plano de dados que são arquivadas em uma conta de armazenamento é configurado no portal quando a configuração **DataPlaneRequests de log** é selecionada. Para alterar essa configuração, consulte [ativar o registro em log na portal do Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Exibir logs em logs de Azure Monitor

Se você tiver selecionado a opção **Enviar para log Analytics** ao ativar o log de diagnóstico, os dados de diagnóstico do seu contêiner serão encaminhados para Azure monitor logs dentro de duas horas. Ao examinar Azure Monitor logs imediatamente depois de ativar o registro em log, você não verá nenhum dado. Basta esperar duas horas e tentar novamente. 

Antes de exibir seus logs, verifique e veja se seu espaço de trabalho do Log Analytics foi atualizado para usar a nova linguagem de consulta Kusto. Para verificar, abra o [portal do Azure](https://portal.azure.com), selecione **log Analytics espaços de trabalho** na extrema esquerda e, em seguida, selecione o nome do espaço de trabalho, conforme mostrado na próxima imagem. A página do **espaço de trabalho log Analytics** é exibida:

![Azure Monitor logs no portal do Azure](./media/logging/azure-portal.png)

>[!NOTE]
>As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.  

Se você vir a seguinte mensagem na página **log Analytics espaço de trabalho** , seu espaço de trabalho não foi atualizado para usar o novo idioma. Para obter mais informações sobre como atualizar para a nova linguagem de consulta, consulte [atualizar seu espaço de trabalho do Azure log Analytics para a nova pesquisa de logs](../log-analytics/log-analytics-log-search-upgrade.md). 

![Mensagem de atualização de logs de Azure Monitor](./media/logging/upgrade-notification.png)

Para exibir os dados de diagnóstico em logs de Azure Monitor, abra a página **pesquisa de logs** no menu à esquerda ou na área de **Gerenciamento** da página, conforme mostrado na imagem a seguir:

![Opções de pesquisa de log no portal do Azure](./media/logging/log-analytics-open-log-search.png)

Agora que você habilitou a coleta de dados, execute o seguinte exemplo de pesquisa de log usando a nova linguagem de consulta para ver os 10 logs mais recentes `AzureDiagnostics | take 10`.

![Pesquisa de log de exemplo para os 10 logs mais recentes](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="cosmosdb-log-analytics-queries-in-azure-monitor"></a>CosmosDB Log Analytics consultas em Azure Monitor

Aqui estão algumas consultas adicionais que você pode inserir na caixa de **pesquisa de log** para ajudá-lo a monitorar seus contêineres de Cosmos do Azure. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md).  

Para saber mais sobre o significado dos dados retornados por cada pesquisa de log, consulte [interpretar seus logs de Azure Cosmos DB](#interpret).

* Para consultar todos os logs de diagnóstico de Azure Cosmos DB por um período de tempo especificado:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Para consultar os 10 eventos registrados mais recentemente:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Para consultar todas as operações, agrupadas por tipo de operação:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Para consultar todas as operações, agrupadas por **recurso**:

    ```
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

* Para consultar todas as atividades do usuário, agrupadas por recurso:

    ```
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
    > [!NOTE]
    > Esse comando é para um log de atividades, não um log de diagnóstico.

* Para obter todas as consultas com mais de 100 RUs Unidas com dados de DataPlaneRequests e QueryRunTimeStatistics

    ```
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```
    
      

* Para consultar quais operações demoram mais do que 3 milissegundos:

    ```
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar qual agente está executando as operações:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Para consultar quando as operações de longa execução foram executadas:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | render timechart
    ```
    
* Para obter estatísticas de chave de partição para avaliar a distorção entre as três principais partições para a conta de banco de dados:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    
   
    ```
    

Para obter mais informações sobre como usar a nova linguagem de pesquisa de logs, consulte [entender pesquisas de logs em logs de Azure monitor](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar seus logs

Os dados de diagnóstico armazenados no armazenamento do Azure e logs de Azure Monitor usam um esquema semelhante. 

A tabela a seguir descreve o conteúdo de cada entrada de log.

| Campo ou Propriedade do armazenamento do Azure | Propriedade de logs de Azure Monitor | Descrição |
| --- | --- | --- |
| **momento** | **TimeGenerated** | A data e hora (UTC) em que a operação ocorreu. |
| **Identificação** | **Recurso** | A conta de Azure Cosmos DB para a qual os logs estão habilitados.|
| **Categorias** | **Categoria** | Para logs de Azure Cosmos DB, **DataPlaneRequests** é o único valor disponível. |
| **operationName** | **OperationName** | Nome da operação. Esse valor pode ser qualquer uma das seguintes operações: criar, atualizar, ler, ReadFeed, excluir, substituir, executar, SQLQuery, consultar, JSQuery, Head, HeadFeed ou Upsert.   |
| **Properties** | n/d | O conteúdo deste campo é descrito nas linhas a seguir. |
| **activityId** | **activityId_g** | O GUID exclusivo para a operação registrada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente do usuário cliente que está executando a solicitação. O formato é {nome do agente do usuário}/{versão|
| **requestResourceType** | **requestResourceType_s** | O tipo de recurso acessado. Esse valor pode ser qualquer um dos seguintes tipos de recursos: banco de dados, contêiner, documento, anexo, usuário, permissão, StoredProcedure, gatilho, UserDefinedFunction ou oferta. |
| **statusCode** | **statusCode_s** | O status da resposta da operação. |
| **requestResourceId** | **Identificação** | O ResourceId que pertence à solicitação. O valor pode apontar para databaseRid, collectionRid ou documentRid dependendo da operação executada.|
| **clientIpAddress** | **clientIpAddress_s** | O endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs que são usadas pela operação |
| **collectionRid** | **collectionId_s** | A ID exclusiva da coleção.|
| **permanência** | **duration_s** | A duração da operação, em milissegundos. |
| **requestLength** | **requestLength_s** | O comprimento da solicitação, em bytes. |
| **responseLength** | **responseLength_s** | O comprimento da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Esse valor não é vazio quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são usados para autenticação. O valor aponta para a ID de recurso do usuário. |

## <a name="next-steps"></a>Passos seguintes

- Para entender como habilitar o registro em log e também as métricas e as categorias de log com suporte nos vários serviços do Azure, leia a [visão geral das métricas nos artigos Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md) .
- Leia estes artigos para saber mais sobre os hubs de eventos:
   - [O que são hubs de eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leia [baixar métricas e logs de diagnóstico do armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Leia [entender as pesquisas de log nos logs de Azure monitor](../log-analytics/log-analytics-log-search-new.md).
