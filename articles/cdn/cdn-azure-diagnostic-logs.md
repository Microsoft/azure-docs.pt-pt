---
title: Registos de diagnósticos
titleSuffix: Azure Content Delivery Network
description: Aprenda a usar registos de diagnóstico Azure para salvar análises fundamentais, o que lhe permite exportar métricas de utilização a partir do ponto final da Rede de Entrega de Conteúdos Azure.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: e5d84616e70d2a28abf3937b485f4fcf5258c43e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779412"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Registos de diagnóstico - Rede de Entrega de Conteúdos Azure

Com registos de diagnóstico Azure, pode ver análises fundamentais e guardá-las em um ou mais destinos, incluindo:

* Conta de armazenamento do Azure
* Área de trabalho do Log Analytics
* Azure Event Hubs

Esta funcionalidade está disponível nos pontos finais da CDN para todos os níveis de preços. 

Os registos de diagnóstico permitem exportar métricas básicas de utilização do seu ponto final CDN para diferentes tipos de fontes para que possa consumi-las de forma personalizada. Pode fazer os seguintes tipos de exportação de dados:

* Os dados de exportação para o armazenamento de bolhas, exportação para CSV e gerar gráficos no Excel.
* Os dados de exportação para os Centros de Eventos e correlacionam-se com dados de outros serviços da Azure.
* Dados de exportação para registos do Azure Monitor e visualizar dados no seu próprio espaço de trabalho Log Analytics

É necessário um perfil Azure CDN para os seguintes passos. Consulte para [criar um perfil Estrotivo Azure CDN e ponto final](cdn-create-new-endpoint.md) antes de continuar.

## <a name="enable-logging-with-the-azure-portal"></a>Ativar o registo com o portal do Azure

Siga estes passos para ativar o seu ponto final Azure CDN:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. No portal Azure, navegue para **Todos os recursos** o seu perfil de  ->  **cdn**

2. Selecione o ponto final cdn para o qual deseja ativar registos de diagnóstico:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Selecione o ponto final do CDN." border="true":::

3. Selecione **registos de diagnóstico** na secção **de Monitorização:**

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Selecione o ponto final do CDN." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Ativar o registo com o armazenamento Azure

Para utilizar uma conta de armazenamento para armazenar os registos, siga estes passos:

 >[!NOTE] 
 >É necessária uma conta de armazenamento para completar estes passos. Consulte a: **[Criar uma conta de Armazenamento Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%252fazure%252fstorage%252fblobs%252ftoc.json)** para obter mais informações.
    
1. Para **o nome de definição de diagnóstico,** insira um nome para as definições de registo de diagnóstico.
 
2. Selecione **Archive para uma conta de armazenamento** e, em seguida, selecione **CoreAnalytics** . 

3. Para **retenção (dias)** , escolha o número de dias de retenção. Uma retenção de zero dias armazena os registos indefinidamente. 

4. Selecione a conta de subscrição e armazenamento para os registos.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Selecione o ponto final do CDN." border="true":::

3. Selecione **Guardar** .

### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics

Para utilizar o Log Analytics para os registos, siga estes passos:

>[!NOTE] 
>É necessário um espaço de trabalho de análise de registo para completar estes passos. Consulte: **[Criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md)** para obter mais informações.
    
1. Para **o nome de definição de diagnóstico,** insira um nome para as definições de registo de diagnóstico.

2. Selecione **Enviar para Registar Análise** e, em seguida, selecione **CoreAnalytics** . 

3. Selecione a subscrição e log Analytics espaço de trabalho para os registos.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Selecione o ponto final do CDN." border="true":::

4. Selecione **Guardar** .

### <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos

Para utilizar um centro de eventos para os registos, siga estes passos:

>[!NOTE] 
>É necessário um centro de eventos para completar estes passos. Consulte: **[Quickstart: Crie um centro de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md)** para obter mais informações.
    
1. Para **o nome de definição de diagnóstico,** insira um nome para as definições de registo de diagnóstico.

2. Selecione **Stream para um centro de eventos** e, em seguida, selecione **CoreAnalytics** . 

3. Selecione o espaço de nome de subscrição e de centro de eventos para os registos.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Selecione o ponto final do CDN." border="true":::

4. Selecione **Guardar** .


## <a name="enable-logging-with-powershell"></a>Ativar registo com o PowerShell

O exemplo a seguir mostra como ativar os registos de diagnóstico através dos Cmdlets Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Ativar registos de diagnóstico numa conta de armazenamento

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Para ativar os Registos de Diagnóstico numa conta de armazenamento, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Ativar registos de diagnóstico para log analytics espaço de trabalho

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para ativar registos de diagnóstico para um espaço de trabalho log analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Ativar registos de diagnóstico para o espaço de nome do centro de eventos

1. Inscreva-se no Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Para ativar registos de diagnóstico para um espaço de trabalho log analytics, insira estes comandos. Substitua as variáveis pelos seus valores:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumir registos de diagnóstico do Armazenamento do Microsoft Azure
Esta secção descreve o esquema de análise de núcleo cdN, organização numa conta de armazenamento Azure, e fornece código de amostra para descarregar os registos num ficheiro CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilizando o Microsoft Azure Storage Explorer
Para descarregar a ferramenta, consulte o [Azure Storage Explorer](https://storageexplorer.com/). Depois de descarregar e instalar o software, configure-o para utilizar a mesma conta de armazenamento Azure que foi configurada como um destino para os Registos de Diagnósticos CDN.

1.  Abrir **o Microsoft Azure Storage Explorer**
2.  Localizar a conta de armazenamento
3.  Expanda o nó **de recipientes Blob** nesta conta de armazenamento.
4.  Selecione o recipiente denominado *insights-logs-coreanalytics* .
5.  Os resultados aparecem no painel direito, começando pelo primeiro nível, como *recursosId=* . Continue a selecionar cada nível até encontrar o ficheiro *PT1H.jsligado* . Para obter uma explicação do caminho, consulte o [formato do caminho blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  CadaPT1H.js *blob no* ficheiro representa os registos de análise durante uma hora para um ponto final específico do CDN ou para o seu domínio personalizado.
7.  O esquema do conteúdo deste ficheiro JSON é descrito no esquema de secção dos registos de análise do núcleo.


#### <a name="blob-path-format"></a>Formato do caminho blob

Os registos de análise do núcleo são gerados a cada hora e os dados são recolhidos e armazenados dentro de uma única bolha de Azure como uma carga útil JSON. A ferramenta de explorador de armazenamento interpreta '/' como separador de diretório e mostra a hierarquia. O caminho para a bolha de Azure aparece como se houvesse uma estrutura hierárquica e representasse o nome blob. O nome da bolha segue a seguinte convenção de nomeação:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrição dos campos:**

|Valor|Descrição|
|-------|---------|
|ID da subscrição    |ID da subscrição Azure em formato Guid.|
|Nome do Grupo de Recursos |Nome do grupo de recursos a que pertencem os recursos da CDN.|
|Profile Name (Nome do Perfil) |Nome do Perfil CDN|
|Nome do ponto final |Nome do ponto final do CDN|
|Ano|  Representação de quatro dígitos do ano, por exemplo, 2017|
|Mensal| Representação de dois dígitos do número do mês. 01=Janeiro ... 12=Dezembro|
|Dia|   Representação de dois dígitos do dia do mês|
|PT1H.jsem| Arquivo JSON real onde os dados de análise são armazenados|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportação dos dados de análise de base para um ficheiro CSV

Para aceder à análise do núcleo, é fornecido um código de amostra para uma ferramenta. Esta ferramenta permite descarregar os ficheiros JSON num formato de ficheiro separado por vírgula plana, que pode ser usado para criar gráficos ou outras agregações.

Eis como pode usar a ferramenta:

1.  Visite o link GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Descarregue o código.
3.  Siga as instruções para compilar e configurar.
4.  Executar a ferramenta.
5.  O ficheiro CSV resultante mostra os dados de análise numa hierarquia simples e plana.

## <a name="log-data-delays"></a>Registar atrasos nos dados

A tabela seguinte mostra atrasos de dados de registo para **Azure CDN Standard da Microsoft** , **Azure CDN Standard da Akamai** , e **Azure CDN Standard/Premium da Verizon** .

Atrasos nos dados de registo da Microsoft | Atrasos nos dados de registo de Verizon | Atrasos nos dados do registo da Akamai
--- | --- | ---
Adiado por 1 hora. | Adiado por 1 hora e pode levar até 2 horas para começar a aparecer após a conclusão da propagação do ponto final. | Adiado por 24 horas; se foi criado há mais de 24 horas, leva até 2 horas para começar a aparecer. Se foi criado recentemente, pode levar até 25 horas para os registos começarem a aparecer.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de registo de diagnóstico para análise de núcleos cdN

A Microsoft oferece atualmente apenas registos de análise de núcleo, que contêm métricas que mostram estatísticas de resposta HTTP e estatísticas de saída vistas pelos POPs/edges do CDN.

### <a name="core-analytics-metrics-details"></a>Detalhes das métricas de análise do núcleo
A tabela a seguir mostra uma lista de métricas disponíveis nos registos de análise do núcleo para:

* **Azure CDN Standard da Microsoft**
* **Azure CDN Standard da Akamai**
* **Azure CDN Standard/Premium de Verizon**

Nem todas as métricas estão disponíveis de todos os fornecedores, embora tais diferenças sejam mínimas. A tabela também mostra se uma determinada métrica está disponível de um fornecedor. As métricas estão disponíveis apenas para os pontos finais da CDN que têm tráfego sobre eles.


|Métrica                     | Descrição | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| PedidoCountTotal         | O número total de pedidos é atingido durante este período. | Sim | Sim |Sim |
| PedidoCountHttpStatus2xx | Contagem de todos os pedidos que resultaram num código HTTP 2xx (por exemplo, 200, 202). | Sim | Sim |Sim |
| PedidoCountHttpStatus3xx | Contagem de todos os pedidos que resultaram num código HTTP 3xx (por exemplo, 300, 302). | Sim | Sim |Sim |
| PedidoCountHttpStatus4xx | Contagem de todos os pedidos que resultaram num código HTTP 4xx (por exemplo, 400, 404). | Sim | Sim |Sim |
| PedidoCountHttpStatus5xx | Contagem de todos os pedidos que resultaram num código HTTP 5xx (por exemplo, 500,504). | Sim | Sim |Sim |
| PedidoCountHttpStatusOthers | Contagem de todos os outros códigos HTTP (fora de 2xx-5xx). | Sim | Sim |Sim |
| PedidoCountHttpStatus200 | Contagem de todos os pedidos que resultaram numa resposta de código 200 HTTP. | Sim | Não  |Sim |
| PedidoCountHttpStatus206 | Contagem de todos os pedidos que resultaram numa resposta de código HTTP 206. | Sim | Não  |Sim |
| PedidoCountHttpStatus302 | Contagem de todos os pedidos que resultaram numa resposta de código 302 HTTP. | Sim | Não  |Sim |
| PedidoCountHttpStatus304 | Contagem de todos os pedidos que resultaram numa resposta de código 304 HTTP. | Sim | Não  |Sim |
| PedidoCountHttpStatus404 | Contagem de todos os pedidos que resultaram numa resposta de código 404 HTTP. | Sim | Não  |Sim |
| PedidoCountCacheHit | Contagem de todos os pedidos que resultaram num ataque cache. O ativo foi servido diretamente do POP para o cliente. | Sim | Sim | Não  |
| PedidoCountCacheMiss | Contagem de todos os pedidos que resultaram numa falha cache. Uma falha cache significa que o ativo não foi encontrado no POP mais próximo do cliente, e foi recuperado da origem. | Sim | Sim | Não |
| PedidoCountCacheNoCache | Conte todos os pedidos a um ativo que seja impedido de ser em cache devido a uma configuração do utilizador na borda. | Sim | Sim | Não |
| PedidoCountCacheUncacheable | Conte de todos os pedidos a ativos que são impedidos de serem cacheed pelos cabeçalhos Cache-Control e Expira do ativo. Esta contagem indica que não deve ser em cache num POP ou pelo cliente HTTP. | Sim | Sim | Não |
| PedidoCountCacheOthers | Contagem de todos os pedidos com estatuto de cache não coberto por acima. | Não | Sim | Não  |
| EgressTotal | Transferência de dados de saída em GB | Sim |Sim |Sim |
| EgressHttpStatus2xx | Transferência de dados de saída* para respostas com códigos de estado 2xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus3xx | Transferência de dados de saída para respostas com códigos de estado 3xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus4xx | Transferência de dados de saída para respostas com códigos de estado 4xx HTTP em GB. | Sim | Sim | Não  |
| EgressHttpStatus5xx | Transferência de dados de saída para respostas com códigos de estado 5xx HTTP em GB. | Sim | Sim | Não |
| EgressHttpStatusOthers | Transferência de dados de saída para respostas com outros códigos de estado HTTP em GB. | Sim | Sim | Não  |
| EgressCacheHit | Transferência de dados de saída para respostas que foram entregues diretamente a partir da cache CDN nos POPs/Edges do CDN. | Sim | Sim | Não |
| EgressCacheMiss. | Transferência de dados de saída para respostas que não foram encontradas no servidor POP mais próximo, e recuperadas do servidor de origem. | Sim | Sim | Não |
| EgressCacheNoCache | Transferência de dados de saída para ativos que são impedidos de ser em cache devido a uma configuração do utilizador no limite. | Sim | Sim | Não |
| EgressCacheUncache | Transferência de dados de saída para ativos que são impedidos de ser em cache pelos Cache-Control do ativo e, ou cabeçalhos expira. Indica que não deve ser em cache num POP ou pelo cliente HTTP. | Sim | Sim | Não |
| EgressCacheOthers | Transferências de dados de saída para outros cenários de cache. | Não | Sim | Não |

*Transferência de dados de saída refere-se ao tráfego entregue dos servidores CDN POP ao cliente.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema dos registos de análise do núcleo 

Todos os registos são armazenados no formato JSON e cada entrada tem campos de cordas de acordo com o seguinte esquema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Onde o *tempo* representa a hora de início do limite de horas para o qual as estatísticas são reportadas. Uma métrica não suportada por um fornecedor de CDN, em vez de um valor duplo ou inteiro, resulta num valor nulo. Este valor nulo indica a ausência de uma métrica, e é diferente de um valor de 0. Um conjunto destas métricas por domínio é configurado no ponto final.

Propriedades de exemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Recursos adicionais

* [Registos de diagnóstico Azure](../azure-monitor/platform/platform-logs-overview.md)
* [Análise de núcleo via portal suplementar Azure CDN](./cdn-analyze-usage-patterns.md)
* [Registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)