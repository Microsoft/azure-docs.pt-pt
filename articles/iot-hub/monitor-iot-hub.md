---
title: Monitorar hub Azure IoT
description: Comece aqui para aprender a monitorizar o Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 5e2f5e067f0a1d5c13179b3d6175b3aebf6a43fd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548613"
---
# <a name="monitoring-azure-iot-hub"></a>Monitorar hub Azure IoT

Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelo Azure IoT Hub e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

## <a name="monitor-overview"></a>Visão geral do monitor

A página **de visão geral** no portal Azure para cada hub IoT inclui gráficos que fornecem algumas métricas de utilização, como o número de mensagens usadas e o número de dispositivos ligados ao hub IoT.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Gráficos métricos predefinidos na página de visão geral do hub de IoT.":::

Esta informação é útil, mas representa apenas uma pequena quantidade dos dados de monitorização que estão disponíveis para um hub IoT. Alguns dados de monitorização são recolhidos automaticamente e estão disponíveis para análise assim que criar o seu hub IoT. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Azure IoT Hub cria dados de monitorização utilizando [o Azure Monitor,](/azure/azure-monitor/overview)que é um serviço de monitorização de pilhas completas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local.

Comece com o artigo [Monitorar recursos Azure com Azure Monitor,](/azure/azure-monitor/insights/monitor-azure-resource)que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo, descrevendo os dados específicos recolhidos para o Azure IoT Hub e fornecendo exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitoring-data"></a>Monitorizar dados

O Azure IoT Hub recolhe os mesmos tipos de dados de monitorização que outros recursos Azure que são descritos na [monitorização de dados a partir de recursos Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Consulte [a referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md) para obter informações detalhadas sobre as métricas e registos criados pelo Azure IoT Hub.

> [!IMPORTANT]
> Os eventos emitidos pelo serviço IoT Hub utilizando registos de recursos do Azure Monitor não são garantidos como fiáveis ou encomendados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os registos de recursos também não são feitos para ser em tempo real, e pode levar vários minutos para que os eventos sejam registados à sua escolha de destino.

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de Atividade são recolhidos e armazenados automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico.

Os Registos de Recursos não são recolhidos e armazenados até criar uma definição de diagnóstico e encaminhá-los para um ou mais locais.

As métricas e os registos podem ser encaminhados para um espaço de trabalho log analytics, onde podem ser analisados através de Registos monitores Azure; ao Azure Storage para arquivamento e análise offline; ou para um ponto final do Event Hubs onde podem ser lidos por aplicações externas, por exemplo, ferramentas SIEM de terceiros.

No portal Azure, pode selecionar **as definições** de Diagnóstico em **Monitorização** no painel esquerdo do seu hub IoT seguido por **Adicionar a definição** de diagnóstico para criar definições de diagnóstico scopeed para os registos e métricas da plataforma emitidas pelo seu hub IoT.

A imagem que se segue mostra uma definição de diagnóstico para encaminhar operações de ligação em registos de recursos e todas as métricas da plataforma para um espaço de trabalho Log Analytics.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Gráficos métricos predefinidos na página de visão geral do hub de IoT.":::

Consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](/azure/azure-monitor/platform/diagnostic-settings) para o processo detalhado para criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell. Quando cria uma definição de diagnóstico, especifica quais as categorias de registos a recolher. As categorias de Azure IoT Hub estão listadas nos [registos de recursos na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Ao encaminhar as métricas da plataforma IoT Hub para outros locais, esteja ciente de que:

- As seguintes métricas da plataforma não são exportáveis através de definições de diagnóstico: *Dispositivos conectados (pré-visualização)* e *dispositivos Totais (pré-visualização)* .

- As métricas multidimensionais, por exemplo, [algumas métricas de encaminhamento,](monitor-iot-hub-reference.md#routing-metrics)são atualmente exportadas como métricas unidimensionais achatadas agregadas entre valores de dimensão. Para obter mais detalhes, consulte [as métricas da plataforma de exportação para outros locais.](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para O Azure IoT Hub com métricas de outros serviços Azure usando métricas exploradores abrindo **métricas** a partir do menu **Azure Monitor.** Consulte [o Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) para obter detalhes sobre a utilização desta ferramenta.

No portal Azure, pode selecionar **métricas** em **Monitorização** no painel esquerdo do seu hub IoT para abrir métricas explorer telescópio, por padrão, às métricas da plataforma emitidas pelo seu hub IoT:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Gráficos métricos predefinidos na página de visão geral do hub de IoT.":::

Para obter uma lista das métricas da plataforma recolhidas para o Azure IoT Hub, consulte [métricas na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#metrics). Para obter uma lista das métricas da plataforma recolhidas para todos os serviços Azure, consulte [métricas suportadas com o Azure Monitor](/azure/azure-monitor/platform/metrics-supported).

Para as métricas da plataforma IoT Hub que são recolhidas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis. Para saber mais, consulte [agregações suportadas na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

Algumas métricas do IoT Hub, como [as métricas de encaminhamento,](monitor-iot-hub-reference.md#routing-metrics)são multidimensionais. Para estas métricas, pode aplicar [filtros](/azure-monitor/platform/metrics-charts#apply-filters-to-charts) e [dividir-se](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) nos seus gráficos com base numa dimensão.

## <a name="analyzing-logs"></a>Análise de registos

Os dados em Registos monitores Azure são armazenados em tabelas onde cada mesa tem o seu próprio conjunto de propriedades únicas. Para saber mais sobre os Registos do Monitor Azure, consulte a visão geral dos Registos do [Monitor Azure](/azure/azure-monitor/platform/data-platform-logs) na documentação do Azure Monitor. 

Para encaminhar dados para Registos do Monitor Azure, tem de criar uma definição de diagnóstico para enviar registos de recursos ou métricas de plataforma para um espaço de trabalho do Log Analytics. Para saber mais, consulte [Coleção e encaminhamento.](#collection-and-routing)

No portal Azure, pode selecionar **Registos** em **Monitorização** no painel esquerdo do seu hub IoT para executar consultas de Log Analytics, por padrão, nos registos e métricas recolhidos nos Registos do Monitor Azure para o seu hub IoT.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Gráficos métricos predefinidos na página de visão geral do hub de IoT.":::

Para obter uma lista das tabelas utilizadas pelos Registos do Monitor Azure e consultada por Log Analytics, consulte [as tabelas de registos do Monitor Azure na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Todos os registos de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos de serviço. O esquema comum é delineado no [esquema de registo de recursos do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema). Pode encontrar o esquema e as categorias de registos de recursos recolhidos para O Azure IoT Hub em [registos de recursos na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

O [registo de Atividades](/azure/azure-monitor/platform/activity-log) é um registo de plataforma no Azure que fornece informações sobre eventos de nível de subscrição. Pode vê-lo de forma independente ou encaminhá-lo para Registos do Monitor Azure, onde pode fazer consultas muito mais complexas usando o Log Analytics.  

Ao encaminhar as métricas da plataforma IoT Hub para os Registos do Monitor Azure, esteja ciente de que:

- As seguintes métricas da plataforma não são exportáveis através de definições de diagnóstico: *Dispositivos conectados (pré-visualização)* e *dispositivos Totais (pré-visualização)* .

- As métricas multidimensionais, por exemplo, [algumas métricas de encaminhamento,](monitor-iot-hub-reference.md#routing-metrics)são atualmente exportadas como métricas unidimensionais achatadas agregadas entre valores de dimensão. Para obter mais detalhes, consulte [as métricas da plataforma de exportação para outros locais.](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations)

Para algumas consultas comuns com IoT Hub, consulte [as consultas de Sample Kusto](#sample-kusto-queries). Para obter informações detalhadas sobre a utilização de consultas de Log Analytics, consulte [a visão geral das consultas de registo no Azure Monitor](/azure/azure-monitor/log-query/log-query-overview).

### <a name="sdk-version-in-iot-hub-logs"></a>Versão SDK em registos IoT Hub

Algumas operações em registos de recursos do IoT Hub devolvem uma `sdkVersion` propriedade no seu `properties` objeto. Para estas operações, quando um dispositivo ou app backend está a usar um dos SDKs Azure IoT, esta propriedade contém informações sobre o SDK que está a ser utilizado, a versão SDK e a plataforma em que o SDK está em execução. O exemplo a seguir mostra a `sdkVersion` propriedade emitida para uma [`deviceConnect`](monitor-iot-hub-reference.md#connections) operação quando utiliza o dispositivo de Node.js SDK: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Aqui está um exemplo do valor emitido para o .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

A tabela a seguir mostra o nome SDK usado para diferentes SDK Azure IoT:

| Nome SDK na propriedade SdkVersion | Linguagem |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.dispositivos | .NET (C#) serviço SDK |
| microsoft.azure.devices.client | .NET (C#) dispositivo SDK |
| iothubclient | Dispositivo C ou Python v1 (preceded) SDK |
| iothubserviceclient | Serviço C ou Python v1 (preceded) SDK |
| azure-iot-device-iothub-py | Dispositivo python SDK |
| azure-iot-device | Node.js dispositivo SDK |
| azure-iothub | serviço Node.js SDK |
| com.microsoft.azure.iothub-java-cliente | Dispositivo Java SDK |
| com.microsoft.azure.iothub.service.sdk | Serviço Java SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Dispositivo Java SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Serviço Java SDK |
| C | Incorporado C |
| C + (OSSimplificado = Azure RTOS) | Azure RTOS |

Pode extrair a propriedade da versão SDK quando efetuar consultas contra registos de recursos do IoT Hub. Por exemplo, a seguinte consulta extrai a propriedade da versão SDK (e iD do dispositivo) das propriedades devolvidas pelas operações connections. Estas duas propriedades são escritas para os resultados juntamente com o tempo de funcionamento e o ID de recurso do hub IoT a que o dispositivo está a ligar.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Experimente consultas kusto

> [!IMPORTANT]
> Quando seleciona **Logs** do menu do hub IoT, o Log Analytics é aberto com o âmbito de consulta definido para o atual hub IoT. Isto significa que as consultas de registo só incluirão dados desse recurso. Se pretender executar uma consulta que inclua dados de outros centros IoT ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter mais detalhes.

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar o seu hub IoT.

- Erros de conectividade: Identificar erros de ligação do dispositivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Erros de estrangulamento: Identificar dispositivos que fizeram mais pedidos resultando em erros de estrangulamento.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Dead Endpoints: Identifique pontos finais mortos ou não saudáveis pelo número de vezes que o problema foi relatado, bem como a razão pela qual.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Resumo do erro: Contagem de erros em todas as operações por tipo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Dispositivos recentemente ligados: Lista de dispositivos que o IoT Hub viu ligar no período de tempo especificado.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Versão SDK de dispositivos: Lista de dispositivos e suas versões SDK para ligações de dispositivos ou dispositivo para operações de cloud twin.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Leia registos dos Hubs de Eventos Azure

Depois de configurar o registo de eventos através de definições de diagnóstico, pode criar aplicações que leiam os registos para que possa tomar medidas com base nas informações neles. Este código de amostra obtém registos de um centro de eventos:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas no seu sistema antes que os seus clientes os percebam. Pode definir alertas em [métricas,](/azure/azure-monitor/platform/alerts-metric-overview) [registos](/azure/azure-monitor/platform/alerts-unified-log)e no registo de [atividades](/azure/azure-monitor/platform/activity-log-alerts). Diferentes tipos de alertas têm benefícios e inconvenientes.

Ao criar uma regra de alerta baseada em métricas da plataforma, esteja ciente de que para as métricas da plataforma IoT Hub que são recolhidas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis. Para saber mais, consulte [agregações suportadas na referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="next-steps"></a>Passos seguintes

- Consulte [a referência de dados do Monitor Azure IoT Hub](monitor-iot-hub-reference.md) para uma referência das métricas, registos e outros valores importantes criados por [nome de serviço].

- Consulte [os recursos de Monitor Azure com o Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) para obter informações sobre a monitorização dos recursos do Azure.
