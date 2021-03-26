---
title: Monitorização e exploração madeireira - Azure
description: Este artigo fornece uma visão geral da monitorização e início de sessão em Live Video Analytics no IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564488"
---
# <a name="monitoring-and-logging"></a>Monitorização e registos

Neste artigo, você vai aprender a receber eventos para monitorização remota a partir do módulo Live Video Analytics no módulo IoT Edge. 

Também aprenderá a controlar os registos que o módulo gera.

## <a name="taxonomy-of-events"></a>Taxonomia dos eventos

Live Video Analytics on IoT Edge emite eventos, ou dados de telemetria, de acordo com a seguinte taxonomia:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagrama que mostra a taxonomia dos acontecimentos.":::

* Operacional: Eventos gerados pelas ações de um utilizador ou durante a execução de um [gráfico mediático](media-graph-concept.md)
   
   * Volume: Espera-se que seja baixo (algumas vezes por minuto, ou ainda menos)
   * Exemplos:

      - A gravação começou (mostrada no exemplo seguinte)
      - A gravação parou
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnósticos: Eventos que ajudam a diagnosticar problemas com o desempenho

   * Volume: Pode ser alto (várias vezes por minuto)
   * Exemplos:
   
      - Informações [rtsp SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) (mostradas no exemplo seguinte) 
      - Lacunas no feed de vídeo de entrada

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: Eventos gerados como parte da análise de vídeo

   * Volume: Pode ser alto (várias vezes por minuto ou mais)
   * Exemplos:
      
      - Movimento detetado (mostrado no exemplo seguinte) 
      - Resultado da inferência

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Os eventos emitidos pelo módulo são enviados para o [hub IoT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Podem ser encaminhados de lá para outros destinos. 

### <a name="timestamps-in-analytic-events"></a>Timetamps em eventos analíticos

Como indicado anteriormente, os eventos gerados como parte da análise de vídeo têm os timetamps associados a eles. Se [gravou o vídeo ao vivo](video-recording-concept.md) como parte da topologia do seu gráfico, estes timetamps ajudam-no a localizar onde no vídeo gravado ocorreu o evento em particular. Seguem-se as diretrizes sobre como mapear a data num evento analítico à linha temporal do vídeo gravado num [ativo da Azure Media Services](terminology.md#asset).

Primeiro, extrair o `eventTime` valor. Utilize este valor num [filtro de intervalo](playback-recordings-how-to.md#time-range-filters) de tempo para recuperar uma parte adequada da gravação. Por exemplo, é melhor recuperar o vídeo que começa 30 segundos antes `eventTime` e termina 30 segundos depois. Para o exemplo anterior, onde `eventTime` está 2020-05-12T23:33:09.381Z, um pedido de manifesto HLS durante os 30 segundos antes e depois `eventTime` seria este pedido:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

O URL anterior devolveria uma [lista de reprodução principal](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) que contém URLs para listas de reprodução de mídia. A lista de reprodução de mídia conteria entradas como esta:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
A entrada anterior informa que está disponível um fragmento de vídeo que começa com um `timestamp` valor de `143039375031270` . O `timestamp` valor no evento analítico utiliza o mesmo prazo que a lista de reprodução de mídia. Pode ser usado para identificar o fragmento de vídeo relevante e procurar a moldura correta.

Para obter mais informações, consulte estes [artigos sobre a procura precisa de quadros](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) no HLS.

## <a name="controlling-events"></a>Controlo dos eventos

Pode utilizar as seguintes propriedades gémeas do módulo para controlar os eventos operacionais e de diagnóstico publicados pelo live video analytics no módulo IoT Edge. Estas propriedades estão documentadas no [esquema JSON gémeo do módulo.](module-twin-configuration-schema.md)

- `diagnosticsEventsOutputName`: Para obter eventos de diagnóstico do módulo, inclua esta propriedade e forneça qualquer valor para ele. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos de diagnóstico.
   
- `operationalEventsOutputName`: Para obter eventos operacionais do módulo, inclua esta propriedade e forneça qualquer valor para o mesmo. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos operacionais.
   
Os eventos analíticos são gerados por nós como o processador de deteção de movimentos ou o processador de extensão HTTP. A pia do hub IoT é usada para enviá-los para o hub IoT Edge. 

Pode controlar o [encaminhamento de todos os eventos anteriores](../../iot-edge/module-composition.md#declare-routes) utilizando a `desired` propriedade do módulo twin no manifesto de `$edgeHub` implantação:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

No JSON anterior, é o nome do vídeo `lvaEdge` ao vivo analítico no módulo IoT Edge. A regra de encaminhamento segue o esquema definido nas [rotas de Declarar](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Para garantir que os eventos de análise cheguem ao hub IoT Edge, é necessário ter um nó de afundanço do hub IoT a jusante de qualquer nó do processador de deteção de movimento e/ou qualquer nó do processador de extensão HTTP.

## <a name="event-schema"></a>Esquema de eventos

Os eventos têm origem no dispositivo de borda e podem ser consumidos na borda ou na nuvem. Os eventos gerados pelo Live Video Analytics no IoT Edge estão em conformidade com o [padrão de mensagens de streaming](../../iot-hub/iot-hub-devguide-messages-construct.md) estabelecido por Azure IoT Hub. O padrão consiste em propriedades do sistema, propriedades de aplicação e um corpo.

### <a name="summary"></a>Resumo

Cada evento, quando observado via IoT Hub, tem um conjunto de propriedades comuns:

|Propriedade   |Tipo de propriedade| Tipo de dados   |Descrição|
|---|---|---|---|
|`message-id`   |sistema |guid|  Identificação única do evento.|
|`topic`|   aplicaçãoProperty |string|    Caminho do Gestor de Recursos Azure para a conta Azure Media Services.|
|`subject`| aplicaçãoProperty |string|    Subpata da entidade que emite o evento.|
|`eventTime`|   aplicaçãoProperty|    string| Hora do evento ser gerado.|
|`eventType`|   aplicaçãoProperty |string|    Identificador de tipo de evento. (Ver a seguinte secção.)|
|`body`|body    |objeto|    Dados particulares do evento.|
|`dataVersion`  |aplicaçãoProperty|   string  |{Major}. {Menor}|

### <a name="properties"></a>Propriedades

#### <a name="message-id"></a>mensagem id

Um identificador globalmente único (GUID) para o evento.

#### <a name="topic"></a>tópico

Representa a conta Azure Media Services associada ao gráfico.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

A entidade que está a emitir o evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A `subject` propriedade permite mapear eventos genéricos para o módulo gerador. Por exemplo, para um nome de utilizador ou senha RTSP inválido, o evento gerado estaria `Microsoft.Media.Graph.Diagnostics.ProtocolError` no `/graphInstances/myGraph/sources/myRtspSource` nó.

#### <a name="event-types"></a>Tipos de evento

Os tipos de eventos são atribuídos a um espaço de nome de acordo com este esquema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classes de eventos

|Nome da classe|Description|
|---|---|
|Análise  |Eventos gerados como parte da análise de conteúdo.|
|Diagnóstico    |Eventos que ajudam no diagnóstico de problemas e desempenho.|
|Operacional    |Eventos gerados como parte da operação de recursos.|

Os tipos de eventos são específicos de cada classe de evento.

Exemplos:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Hora do evento

O tempo do evento é formatado numa corda ISO 8601. Representa o momento em que o evento ocorreu.

### <a name="azure-monitor-collection-via-telegraf"></a>Coleção Azure Monitor via Telegraf

Estas métricas serão reportadas a partir do live video analytics no módulo IoT Edge:  

|Nome da métrica|Tipo|Etiqueta|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Medidor|Iothub, edge_device, module_name, graph_topology|Número total de gráficos ativos por topologia.|
|lva_received_bytes_total|Contador|Iothub, edge_device, module_name, graph_topology, graph_instance graph_node|Número total de bytes recebidos por um nó. Suportado apenas para fontes RTSP.|
|lva_data_dropped_total|Contador|Iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Contador de quaisquer dados caídos (eventos, meios de comunicação, e assim por diante).|

> [!NOTE]
> Um [ponto final prometheus](https://prometheus.io/docs/practices/naming/) é exposto no porto 9600 do recipiente. Se nomear o seu vídeo ao vivo Analytics no módulo IoT Edge "LvaEdge", eles poderão aceder às métricas enviando um pedido GET para http://lvaEdge:9600/metrics .   

Siga estes passos para permitir a recolha de métricas do módulo Live Video Analytics no módulo IoT Edge:

1. Crie uma pasta no seu computador de desenvolvimento e vá para essa pasta.

1. Na pasta, crie um `telegraf.toml` ficheiro que contenha as seguintes configurações:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Certifique-se de que substitui as variáveis no ficheiro .toml. As variáveis são denotadas por aparelhos ( `{}` ).

1. Na mesma pasta, crie um Dockerfile que contenha os seguintes comandos:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Utilize comandos Docker CLI para construir o ficheiro Docker e publicar a imagem no seu registo de contentores Azure.
    
   Para obter mais informações sobre a utilização do CLI do Docker para empurrar para um registo de contentores, consulte [Push e puxe imagens do Docker](../../container-registry/container-registry-get-started-docker-cli.md). Para obter mais informações sobre o Registo do Contentor Azure, consulte a [documentação.](../../container-registry/index.yml)


1. Depois de ter concluído o pedido de inscrição para o registo do contentor Azure, adicione o seguinte nó ao seu ficheiro manifesto de implantação:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Certifique-se de que substitui as variáveis no ficheiro manifesto. As variáveis são denotadas por aparelhos ( `{}` ).


   O Azure Monitor pode ser [autenticado através do principal serviço.](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)
        
   O plug-in Azure Monitor Telegraf expõe [vários métodos de autenticação](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Para utilizar a autenticação principal do serviço, detenda estas variáveis ambientais:  
     `AZURE_TENANT_ID`: Especifica o arrendatário para autenticar.  
     `AZURE_CLIENT_ID`: Especifica o ID do cliente da aplicação para utilizar.  
     `AZURE_CLIENT_SECRET`: Especifica o segredo da aplicação para utilizar.  
     
     >[!TIP]
     > Pode dar ao diretor de serviço o papel de **Editor de Métricas de Monitorização.** Siga os passos na **[Create service principal](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** para criar o principal de serviço e atribuir o papel.

1. Após a implantação dos módulos, as métricas aparecerão no Azure Monitor sob um único espaço de nome. Os nomes métricos corresponderão aos emitidos por Prometeu. 

   Neste caso, no portal Azure, vá ao hub IoT e selecione **Métricas** no painel esquerdo. Devia ver as métricas.

### <a name="log-analytics-metrics-collection"></a>Coleção de métricas Log Analytics
Utilizando [o ponto final prometheus](https://prometheus.io/docs/practices/naming/) juntamente com o Log [Analytics,](../../azure-monitor/logs/log-analytics-tutorial.md)pode gerar e [monitorizar métricas](../../azure-monitor/essentials/metrics-supported.md) como CPUPercent, MemoryUsedPercent, etc.   

> [!NOTE]
> A configuração abaixo não recolhe registos, **apenas métricas**. É possível estender o módulo de coletor para recolher e carregar registos.

[![Diagrama que mostra a coleção de métricas usando Log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Saiba como [recolher métricas](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Utilize comandos Docker CLI para construir o [ficheiro Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) e publicar a imagem no seu registo de contentores Azure.
    
   Para obter mais informações sobre a utilização do CLI do Docker para empurrar para um registo de contentores, consulte [Push e puxe imagens do Docker](../../container-registry/container-registry-get-started-docker-cli.md). Para obter mais informações sobre o Registo do Contentor Azure, consulte a [documentação.](../../container-registry/index.yml)

1. Após a entrada para o registo do contentor Azure, é inserido o seguinte no manifesto de implantação:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Os módulos `edgeHub` , e são os `edgeAgent` `lvaEdge` nomes dos módulos definidos no ficheiro manifesto de implantação. Por favor, certifique-se de que os nomes dos módulos coincidem.   

    Pode obter os seus `LogAnalyticsWorkspaceId` e `LogAnalyticsSharedKey` valores seguindo estes passos:
    1. Vá ao portal Azure
    1. Procure os seus espaços de trabalho Log Analytics
    1. Assim que encontrar o seu espaço de trabalho Log Analytics, navegue para a `Agents management` opção no painel de navegação esquerdo.
    1. Encontrará o ID do Espaço de Trabalho e as Chaves Secretas que pode usar.

1. Em seguida, crie um livro clicando no `Workbooks` separador no painel de navegação esquerdo.
1. Utilizando a linguagem de consulta Kusto, pode escrever consultas como abaixo e obter a percentagem de CPU usada pelos módulos IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagrama que mostra as métricas usando a consulta de Kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Registo

Tal como acontece com outros módulos IoT Edge, também pode [examinar os registos do contentor](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) no dispositivo de borda. Pode configurar as informações escritas nos registos utilizando as seguintes propriedades gémeas do [módulo:](module-twin-configuration-schema.md)

* `logLevel`

   * Os valores permitidos `Verbose` `Information` `Warning` são, `Error` , `None` e.
   * O valor predefinido é `Information`. Os registos contêm erros, avisos e mensagens de informação.
   * Se definir o valor `Warning` para , os registos contêm erros e mensagens de aviso.
   * Se definir o valor para `Error` , os registos contêm apenas mensagens de erro.
   * Se definir o valor para `None` , não serão gerados registos. (Não recomendamos esta configuração.)
   * `Verbose`Utilize apenas se precisar de partilhar registos com suporte Azure para diagnosticar um problema.

* `logCategories`

   * Uma lista separada por vírgulas de um ou mais destes valores: `Application` `Events` . . `MediaPipeline` .
   * O valor predefinido é `Application, Events`.
   * `Application`: Informações de alto nível do módulo, como mensagens de arranque de módulos, erros ambientais e chamadas de métodos diretos.
   * `Events`: Todos os eventos descritos anteriormente neste artigo.
   * `MediaPipeline`: Registos de baixo nível que podem oferecer informações quando se está a resolver problemas, como dificuldades em estabelecer uma ligação com uma câmara capaz de RTSP.
   
### <a name="generating-debug-logs"></a>Gerar registos de depurg

Em certos casos, para ajudar o suporte do Azure a resolver um problema, poderá ter de gerar registos mais detalhados do que os descritos anteriormente. Para gerar estes registos:

1. [Ligue o armazenamento do módulo ao armazenamento do dispositivo](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via `createOptions` . Se olhar para um modelo de manifesto de [implantação](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) a partir dos arranques rápidos, verá este código:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Este código permite que o módulo Edge escreva registos para a trajetória de armazenamento do dispositivo `/var/local/mediaservices/` . 

 1. Adicione a seguinte `desired` propriedade ao módulo:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

O módulo irá agora escrever registos de depuração num formato binário para o caminho de armazenamento do dispositivo `/var/local/mediaservices/debuglogs/` . Pode partilhar estes registos com o suporte do Azure.

## <a name="faq"></a>FAQ

Se tiver dúvidas, consulte a [monitorização e as métricas faq.](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Passos seguintes

[Gravação de vídeo contínua](continuous-video-recording-tutorial.md)