---
title: Registo de diagnósticos no Azure Cosmos DB
description: Saiba mais sobre as diferentes formas de registar e monitorizar dados armazenados no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: c6796d91835669ba174a866eb3c014e71549c0f2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877967"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Registo de diagnósticos no Azure Cosmos DB 

Depois de começar a utilizar um ou mais bases de dados do Azure Cosmos DB, pode querer monitorizar como e quando as suas bases de dados são acedidos. Este artigo fornece uma descrição geral dos registos que estão disponíveis na plataforma do Azure. Saiba como ativar o registo de diagnóstico para efeitos de enviar registos para de monitorização [armazenamento do Azure](https://azure.microsoft.com/services/storage/), como transmitir registos para [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/)e como exportar registos para [deregistosdoAzureMonitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-available-in-azure"></a>Registos disponíveis no Azure

Antes de falarmos sobre como monitorizar a sua conta do Azure Cosmos DB, vamos esclarecer algumas coisas sobre o registo e monitorização. Existem diferentes tipos de registos na plataforma do Azure. Existem [registos de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventos, monitorização heartbeat, registos de operações e assim por diante. Há uma grande quantidade de registos. Pode ver a lista completa de registos no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) no portal do Azure. 

A imagem seguinte mostra os diferentes tipos de registos do Azure que estão disponíveis:

![Diferentes tipos de registos do Azure](./media/logging/azurelogging.png)

Na imagem, o **recursos de computação** representam os recursos do Azure para o qual pode acessar o SO convidado do Microsoft. Por exemplo, conjuntos, Azure Container Service, de dimensionamento de máquinas virtuais do Azure, máquina virtual e assim por diante, são recursos de computação considerados. Recursos de geram registos de atividades, registos de diagnóstico e registos de aplicações de computação. Para obter mais informações, consulte a [origens de dados no Azure de monitorização](../azure-monitor/platform/data-sources.md) artigo.

O **recursos de computação não** são recursos em que não é possível acessar o sistema operacional subjacente e trabalhar diretamente com o recurso. Por exemplo, grupos de segurança de rede, aplicações lógicas e assim por diante. O Azure Cosmos DB é um recurso de não-compute. Pode ver registos de recursos de computação-não no registo de atividades ou ative a opção de registos de diagnóstico no portal. Para obter mais informações, consulte a [origens de dados no Azure Monitor](../azure-monitor/platform/data-sources.md) artigo.

O registo de atividades regista as operações ao nível da subscrição para o Azure Cosmos DB. Operações como ListKeys, escrever DatabaseAccounts e muito mais são registadas. Os registos de diagnóstico fornecem logon mais granular e permitem-lhe registar DataPlaneRequests (Create, Read, consulta e assim por diante) e MongoRequests.


Neste artigo, vamos nos concentrar no registo de atividades do Azure, os registos de diagnóstico do Azure e métricas do Azure. O que é a diferença entre esses três logs? 

### <a name="azure-activity-log"></a>Registo de atividades do Azure

O registo de atividades do Azure é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. O registo de atividades relatórios do plano de controlo de eventos para as suas subscrições sob a categoria administrativa. Pode usar o registo de atividades para determinar o "o quê, quem e quando" para qualquer operação (PUT, POST, DELETE) nos recursos de escrita na sua subscrição. Também é possível compreender o estado da operação e outras propriedades relevantes. 

O registo de atividades é diferente de registos de diagnóstico. O registo de atividades fornecem dados sobre as operações num recurso de fora (a _plano de controlo_). No contexto do Azure Cosmos DB, o plano de controlo operações incluem criar contentor, chaves de lista, eliminar chaves, base de dados de lista e assim por diante. Os registos de diagnóstico são emitidos por um recurso e fornecer informações sobre o funcionamento desse recurso (a _plano de dados_). Alguns exemplos de operações de plano de dados no log de diagnóstico são Delete, Insert e ReadFeed.

Registos de Atividades (operações do painel de controle) podem ser mais avançados por natureza e podem incluir o endereço de e-mail completo do chamador, endereço IP do chamador, nome do recurso, nome da operação, TenantId e muito mais. O registo de atividades contém vários [categorias](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) de dados. Para todos os detalhes sobre esquemas uma destas categorias, consulte [esquema de eventos de registo de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). No entanto, os registos de diagnóstico pode ser restritivos por natureza, os dados pessoais é, muitas vezes, removidos destes registos. Poderá ter o endereço IP do chamador, mas o último octant é removido.

### <a name="azure-metrics"></a>Métrica do Azure

[Métricas do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) tem o tipo mais importante de dados de telemetria do Azure (também denominado _contadores de desempenho_) que é emitida por recursos mais do Azure. Métricas permitem-lhe ver informações sobre o débito, armazenamento, consistência, disponibilidade e a latência dos seus recursos do Azure Cosmos DB. Para obter mais informações, consulte [monitorização e depuração com métricas na Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Registos de diagnóstico do Azure são emitidos por um recurso e fornecer dados avançados e frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso. Os registos de diagnóstico ao nível de recursos também diferem dos registos de diagnóstico de nível de sistema operacional convidado. Os registos de diagnóstico do SO convidado são recolhidos por um agente que está a ser executado dentro de uma máquina virtual ou outro suporte tipo de recurso. Os registos de diagnóstico ao nível de recursos exigem sem agente e a captura de dados de recursos específicos da própria plataforma do Azure. Registos de diagnóstico de nível de sistema operacional convidado capturam os dados do sistema operativo e aplicações em execução numa máquina virtual.

![Registo de diagnósticos para registos de armazenamento, os Hubs de eventos ou do Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>O que é registado por registos de diagnóstico do Azure?

* Todos os pedidos de back-end autenticado (TCP/REST) em todas as APIs são registados, incluindo pedidos falhados resultantes de permissões de acesso, erros de sistema ou pedidos incorretos. Suporte para solicitações de gráfico, Cassandra e API de tabela iniciada pelo utilizador não estão atualmente disponíveis.
* Operações na base de dados em si, que incluem operações CRUD em todos os documentos, contentores e bases de dados.
* Operações de chaves de conta, que incluem a criar, modificar ou eliminar as chaves.
* Pedidos não autenticados que resultam numa resposta 401. Por exemplo, pedidos que não tem um token de portador ou incorretamente formulados ou expirados ou com um token inválido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Ativar o registo no portal do Azure

Para ativar o registo de diagnósticos, tem de ter os seguintes recursos:

* Uma existente do Azure Cosmos DB conta, base de dados e contentores. Para obter instruções sobre como criar esses recursos, consulte [criar uma conta de base de dados com o portal do Azure](create-sql-api-dotnet.md#create-a-database-account), [exemplos da CLI do Azure](cli-samples.md), ou [exemplos do PowerShell](powershell-samples.md).

Para ativar o registo de diagnóstico no portal do Azure, siga os passos abaixo:

1. Na [portal do Azure](https://portal.azure.com), no seu Azure Cosmos DB da conta, selecione **registos de diagnóstico** na navegação à esquerda e, em seguida, selecione **ativar diagnósticos**.

    ![Ativar o registo de diagnósticos para o Azure Cosmos DB no portal do Azure](./media/logging/turn-on-portal-logging.png)

2. Na **das definições de diagnóstico** página, efetue os seguintes passos: 

    * **Nome**: Introduza um nome para os registos criar.

    * **Arquivo para uma conta de armazenamento**: Para utilizar esta opção, terá de uma conta de armazenamento existente para ligar a. Para criar uma nova conta de armazenamento no portal, veja [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) e siga as instruções para criar um Azure Resource Manager, conta para fins gerais. Em seguida, regresse a esta página no portal para selecionar a sua conta de armazenamento. Poderá demorar alguns minutos para as contas de armazenamento recentemente criada aparece no menu pendente.
    * **Stream para um hub de eventos**: Para utilizar esta opção, terá dos Hubs de eventos espaço de nomes e o evento hub existente para ligar a. Para criar um espaço de nomes de Hubs de eventos, consulte [para criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, regresse a esta página no portal para selecionar o nome de espaço de nomes e a política de Hubs de eventos.
    * **Enviar para o Log Analytics**: Para utilizar esta opção, utilize uma área de trabalho existente ou crie uma nova área de trabalho do Log Analytics ao seguir os passos para [criar uma nova área de trabalho](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) no portal. Para obter mais informações sobre como ver os registos nos logs de Monitor do Azure, veja a que vista de registos nos registos do Azure Monitor.
    * **Inicie sessão DataPlaneRequests**: Selecione esta opção para registar pedidos de back-end do Azure Cosmos DB distribuída plataforma subjacente para contas SQL, de gráficos, MongoDB, Cassandra e API de tabela. Se estiver arquivando para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são eliminados automaticamente após o período de retenção expira.
    * **Inicie sessão MongoRequests**: Selecione esta opção para registar pedidos iniciada pelo utilizador a partir do Azure Cosmos DB front-end para satisfazer as necessidades contas do Cosmos configuradas com a API do Azure Cosmos DB para o MongoDB. Se estiver arquivando para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são eliminados automaticamente após o período de retenção expira.
    * **Pedidos de métrica**: Selecione esta opção para armazenar dados detalhados no [métricas do Azure](../azure-monitor/platform/metrics-supported.md). Se estiver arquivando para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são eliminados automaticamente após o período de retenção expira.

3. Selecione **Guardar**.

    Se receber um erro que diz "Falha ao atualizar diagnósticos para \<nome de área de trabalho >. A subscrição \<id da subscrição > não está registada para utilizar o Microsoft. insights, "siga os [resolver problemas relacionados com o Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instruções para registar a conta e, em seguida, repita este procedimento.

    Se pretender alterar a forma como em que seus registos de diagnóstico são guardados no futuro em qualquer momento, regresse a esta página para modificar as definições de registo de diagnóstico para a sua conta.

## <a name="turn-on-logging-by-using-azure-cli"></a>Ativar o registo com a CLI do Azure

Para ativar as métricas e registo de diagnósticos através da utilização da CLI do Azure, utilize os seguintes comandos:

- Para ativar o armazenamento dos registos de diagnóstico numa conta de armazenamento, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   O `resourceId` é o nome da conta do Azure Cosmos DB. O `storageId` é o nome da conta do storage para o qual pretende enviar os registos.

- Para ativar a transmissão em fluxo dos registos de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   O `resourceId` é o nome da conta do Azure Cosmos DB. O `serviceBusRuleId` é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para ativar registos de diagnóstico de envio para uma área de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registo com o PowerShell

Para ativar o registo de diagnósticos com o PowerShell, precisa do Azure Powershell com uma versão mínima 1.0.1.

Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se já tiver instalado o Azure PowerShell e não souber a versão do tipo de consola do PowerShell `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ligar às suas subscrições
Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzureRmAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições associadas com esta conta e, por predefinição, utiliza a primeira.

Se tiver mais de uma subscrição, poderá ter de especificar a subscrição específica que foi utilizada para criar o seu Cofre de chaves do Azure. Para ver as subscrições da sua conta, escreva o seguinte comando:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a subscrição que está associada à conta do Azure Cosmos DB que está fazendo logon, escreva o seguinte comando:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se tiver mais do que uma subscrição que está associada à sua conta, é importante especificar a subscrição que pretende utilizar.
>
>

Para obter mais informações sobre como configurar o Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Criar uma nova conta de armazenamento para os seus registos
Apesar de poder utilizar uma conta de armazenamento existente para os seus registos, neste tutorial, vamos criar uma nova conta de armazenamento dedicada aos registos do Azure Cosmos DB. Para sua comodidade, os detalhes da conta de armazenamento que armazenamos numa variável chamada **sa**.

Para facilitar ainda mais a gestão, neste tutorial, utilizamos o mesmo grupo de recursos como aquela que contém a base de dados do Azure Cosmos DB. Substitua os valores para o **ContosoResourceGroup**, **contosocosmosdblogs**, e **e.u.a. Centro-Norte** parâmetros, conforme aplicável:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se optar por utilizar uma conta de armazenamento existente, a conta tem de utilizar a mesma subscrição que da subscrição do Azure Cosmos DB. A conta também tem de utilizar o modelo de implementação do Resource Manager, em vez do modelo de implementação clássica.
>
>

### <a id="identify"></a>Identificar a conta do Azure Cosmos DB para os seus registos
Defina o nome da conta do Azure Cosmos DB como uma variável chamada **conta**, onde **ResourceName** é o nome da conta do Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Ativar registo
Para ativar o registo para o Azure Cosmos DB, utilize o `Set-AzureRmDiagnosticSetting` cmdlet com variáveis para a nova conta de armazenamento, conta do Azure Cosmos DB e a categoria para ativar o registo. Execute o seguinte comando e defina a **-ativado** sinalizador para **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

A saída do comando deve assemelhar-se o exemplo a seguir:

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

A saída do comando confirma que o registo está agora ativado para a base de dados e informações de sua conta de armazenamento está a ser guardadas.

Opcionalmente, também pode definir a política de retenção para os seus registos, que os registos mais antigos são automaticamente eliminados. Por exemplo, definir a política de retenção com o **- RetentionEnabled** sinalizador definido como **$true**. Definir o **- RetentionInDays** parâmetro para **90** para que os registos com mais de 90 dias são eliminados automaticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Aceder aos seus registos
Registos do Azure Cosmos DB para o **DataPlaneRequests** categoria são armazenadas no **insights-logs--plano-pedidos de dados** contentor na conta de armazenamento fornecida. 

Primeiro, crie uma variável para o nome do contentor. A variável é utilizada em todo a passo a passo.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para listar todos os blobs neste contentor, escreva:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

A saída do comando deve assemelhar-se o exemplo a seguir:

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

Como pode ver a partir desta saída, os blobs seguem uma convenção de nomenclatura: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Uma vez que a mesma conta de armazenamento pode ser utilizada para recolher registos para vários recursos, pode utilizar o ID de recurso completamente qualificado no nome do blob para acessar e baixar os blobs específicos que precisa. Antes de o fazermos, vamos abordar como transferir todos os blobs.

Primeiro, crie uma pasta para transferir os blobs. Por exemplo:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Em seguida, obter uma lista de todos os blobs:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Encaminhe esta lista através do `Get-AzureStorageBlobContent` comando para transferir os blobs para a pasta de destino:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando executar este segundo comando, o **/** delimitador dos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Esta estrutura de pastas é utilizada para transferir e armazenar os blobs como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver várias bases de dados e pretender transferir registos para apenas uma base de dados com o nome **CONTOSOCOSMOSDB3**, utilize o comando:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se tiver vários grupos de recursos e pretender transferir registos para apenas um grupo de recursos, utilize o comando `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Se pretende transferir todos os registos para o mês de Julho de 2017, utilize o comando `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Também pode executar os seguintes comandos:

* Para consultar o estado das definições de diagnóstico para o seu recurso de base de dados, utilize o comando `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Para desativar o registo do **DataPlaneRequests** categoria para o seu recurso de conta de base de dados, utilize o comando `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Os blobs que são devolvidos em cada uma dessas consultas são armazenados como texto e formatados como um blob JSON, conforme mostrado no seguinte código:

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

Para saber mais sobre os dados em cada blob JSON, veja [interpretar os registos do Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Gerir os seus registos

Os registos de diagnóstico são disponibilizados na sua conta de duas horas a partir do momento em que a operação do Azure Cosmos DB foi efetuada. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos de controle de acesso do Azure standard para proteger os seus registos e restringir quem podem acessá-los.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* O período de retenção para solicitações de plano de dados que são arquivados para uma conta de armazenamento está configurado no portal quando o **Log DataPlaneRequests** definição está selecionada. Para alterar essa definição, consulte [ativar o registo no portal do Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Vista regista nos registos do Azure Monitor

Se tiver selecionado o **enviar para o Log Analytics** opção quando está ativado no registo de diagnósticos, diagnóstico dados a partir do seu contentor são reencaminhados para registos do Azure Monitor nas duas horas. Quando analisar registos do Azure Monitor imediatamente depois de ativar o registo, não verá quaisquer dados. Apenas Aguarde duas horas e tente novamente. 

Antes de ver os seus registos, verifique e veja se a sua área de trabalho do Log Analytics foi atualizada para utilizar a nova linguagem de consulta de Kusto. Para verificar, abra a [portal do Azure](https://portal.azure.com), selecione **áreas de trabalho do Log Analytics** na extrema esquerda, selecione o nome de área de trabalho conforme mostrado na imagem seguinte. O **área de trabalho do Log Analytics** é apresentada a página:

![Registos de Monitor do Azure no portal do Azure](./media/logging/azure-portal.png)

>[!NOTE]
>As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.  

Se vir a mensagem seguinte no **área de trabalho do Log Analytics** página, sua área de trabalho não foi atualizada para utilizar a nova linguagem. Para obter mais informações sobre como atualizar para a nova linguagem de consulta, consulte [atualizar a sua área de trabalho do Log Analytics do Azure para a nova pesquisa de registo](../log-analytics/log-analytics-log-search-upgrade.md). 

![Mensagem de atualizar os registos de Monitor do Azure](./media/logging/upgrade-notification.png)

Para ver os dados de diagnóstico nos registos do Azure Monitor, abra a **pesquisa de registos** página no menu à esquerda ou o **gestão** área da página, conforme mostrado na imagem seguinte:

![Opções de pesquisa de registo no portal do Azure](./media/logging/log-analytics-open-log-search.png)

Agora que ativou a recolha de dados, executar o exemplo de pesquisa de registo seguinte ao utilizar a nova linguagem de consulta para ver os registos mais recentes 10 `AzureDiagnostics | take 10`.

![Pesquisa de registos de exemplo para 10 registos mais recentes](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Consultas

Aqui estão algumas consultas adicionais que pode introduzir para o **pesquisa de registos** caixa para o ajudar a monitorizar os seus contentores do Azure Cosmos DB. Estas consultas trabalham com o [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md). 

Para saber mais sobre o significado dos dados que são devolvidos por cada pesquisa de registos, consulte [interpretar os registos do Azure Cosmos DB](#interpret).

* Para consultar todos os registos de diagnóstico do Azure Cosmos DB durante um período de tempo especificado:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Para consultar o 10 mais recentemente registados eventos:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Para consultar todas as operações, agrupadas por tipo de operação:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* A consulta para todas as operações, agrupados por **recursos**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Para consultar todas as atividades de utilizador, agrupadas pelo recurso:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Este comando é para um registo de atividades, não um registo de diagnóstico.

* A consulta para os quais operações mais demoram 3 milissegundos:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar para o qual o agente está a executar as operações:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Para consultar quando foram efetuadas as operações de longa execução:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Para obter mais informações sobre como utilizar a nova linguagem de pesquisa de registos, consulte [pesquisas de registos de compreender nos registos do Azure Monitor](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar os seus registos

Dados de diagnóstico que estão armazenados nos registos de armazenamento e o Azure Monitor do Azure utilizam um esquema semelhante. 

A tabela seguinte descreve o conteúdo de cada entrada de registo.

| Campo de armazenamento do Azure ou a propriedade | Propriedade de registos de Monitor do Azure | Descrição |
| --- | --- | --- |
| **tempo** | **TimeGenerated** | A data e hora (UTC) em que ocorreu a operação. |
| **resourceId** | **Recurso** | A conta do Azure Cosmos DB para o qual os registos estão ativados.|
| **category** | **Categoria** | Para os registos do Azure Cosmos DB, **DataPlaneRequests** é o valor só estão disponível. |
| **operationName** | **OperationName** | Nome da operação. Este valor pode ser qualquer uma das seguintes operações: Criar, atualizar, ler, ReadFeed, eliminação, substituição, executar, SqlQuery, consulta, JSQuery, Head, HeadFeed ou Upsert.   |
| **Propriedades** | n/d | O conteúdo deste campo é descrito nas linhas que se seguem. |
| **activityId** | **activityId_g** | O GUID exclusivo para a operação com sessão iniciada. |
| **userAgent** | **userAgent_s** | Uma cadeia de caracteres que especifica o agente de utilizador do cliente que está a efetuar o pedido. O formato é {nome do agente de utilizador} / {version}.|
| **resourceType** | **ResourceType** | O tipo de recurso acedido. Este valor pode ser qualquer um dos seguintes tipos de recurso: Base de dados, contentores, documentos, anexo, utilizador, permissão, StoredProcedure, acionador, UserDefinedFunction ou oferta. |
| **statusCode** | **statusCode_s** | O estado de resposta da operação. |
| **requestResourceId** | **ResourceId** | O resourceId que diz respeito ao pedido. O valor pode apontar para databaseRid, collectionRid ou documentRid dependendo da operação efetuada.|
| **clientIpAddress** | **clientIpAddress_s** | Endereço IP do cliente. |
| **requestCharge** | **requestCharge_s** | O número de RUs que são utilizados pela operação |
| **collectionRid** | **collectionId_s** | O ID exclusivo para a coleção.|
| **Duração** | **duration_s** | A duração da operação, em tiques. |
| **requestLength** | **requestLength_s** | O comprimento do pedido, em bytes. |
| **responseLength** | **responseLength_s** | O comprimento da resposta, em bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Este valor é não vazia quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são utilizados para autenticação. O valor aponta para o ID de recurso do utilizador. |

## <a name="next-steps"></a>Passos Seguintes

- Para compreender como ativar o registo e também as categorias de métricas e registos que são suportadas por vários serviços do Azure, leia ambos os [descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [descrição geral do Azure os registos de diagnóstico ](../azure-monitor/platform/diagnostic-logs-overview.md) artigos.
- Leia os seguintes artigos para saber mais sobre os hubs de eventos:
   - [O que é o Event Hubs do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leia [transferir registos de diagnóstico e métricas do armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Leia [pesquisas de registos de compreender nos registos do Azure Monitor](../log-analytics/log-analytics-log-search-new.md).
