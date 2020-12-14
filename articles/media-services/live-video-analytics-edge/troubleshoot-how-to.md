---
title: Resolução de problemas Vídeo Analytics em IoT Edge - Azure
description: Este artigo cobre etapas de resolução de problemas para Live Video Analytics no IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: 31cf89cb66dfbc404d65f8fc09b96c03e1be2f8f
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401320"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Resolução de problemas Vídeo Analytics em IoT Edge

Este artigo abrange etapas de resolução de problemas para Live Video Analytics (LVA) em Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

### <a name="diagnostics"></a>Diagnóstico

Como parte da sua implementação live video analytics, você configura recursos Azure, tais como ioT Hub e dispositivos IoT Edge. Como primeiro passo para diagnosticar problemas, certifique-se sempre de que o dispositivo Edge está devidamente configurado seguindo estas instruções:

1. [Executar o `check` comando.](../../iot-edge/troubleshoot.md#run-the-check-command)
1. [Verifique a sua versão IoT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Verifique o estado do gestor de segurança IoT Edge e seus registos](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Veja as mensagens que estão a passar pelo hub IoT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Reiniciar os recipientes](../../iot-edge/troubleshoot.md#restart-containers).
1. [Verifique as regras de firewall e configuração da porta](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Questões de pré-implantação

Se a infraestrutura de borda estiver bem, pode procurar problemas com o ficheiro manifesto de implantação. Para implementar o módulo Live Video Analytics no módulo IoT Edge no dispositivo IoT Edge ao lado de quaisquer outros módulos IoT, utilize um manifesto de implantação que contenha o hub IoT Edge, o agente IoT Edge e outros módulos e suas propriedades. Pode utilizar o seguinte comando para implantar o ficheiro manifesto:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Se o código JSON não estiver bem formado, poderá receber o seguinte erro:   
&nbsp;&nbsp;&nbsp;**Não analisou json do ficheiro: ' <deployment manifest.json> para o argumento 'conteúdo', com exceção: "Dados extra: linha 101 coluna 1 (char 5325)"**

Se encontrar este erro, recomendamos que verifique se o JSON está em falta ou outros problemas com a estrutura do ficheiro. Para validar a estrutura do ficheiro, pode utilizar um cliente como o [Notepad++ com plug-in do Visualizador JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online como o [JSON Formatter & Validador](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante a implementação: Diagnosticar com métodos diretos de gráfico de mídia 

Depois de o módulo Live Video Analytics no IoT Edge ser implantado corretamente no dispositivo IoT Edge, pode criar e executar o gráfico de mídia invocando [métodos diretos](direct-methods.md).  
>[!NOTE]
>  As chamadas de método direto devem ser feitas apenas para o **`lvaEdge`** módulo.

Pode utilizar o portal Azure para executar um diagnóstico do gráfico de mídia utilizando métodos diretos:

1. No portal Azure, vá ao hub IoT que está ligado ao seu dispositivo IoT Edge.

1. Procure a **gestão automática do dispositivo** e, em seguida, selecione **IoT Edge**.  

1. Na lista de dispositivos Edge, selecione o dispositivo que pretende diagnosticar.  
         
    ![Screenshot do portal Azure mostrando uma lista de dispositivos Edge](./media/troubleshoot-how-to/lva-sample-device.png)


1. Verifique se o código de resposta é *200-OK*. Outros códigos de resposta para o [tempo de execução IoT Edge](../../iot-edge/iot-edge-runtime.md) incluem:
    * 400 - A configuração de implantação é mal formada ou inválida.
    * 417 - O dispositivo não tem um conjunto de configuração de implantação.
    * 412 - A versão de esquema na configuração de implementação é inválida.
    * 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
    * 500 - Ocorreu um erro no tempo de execução do IoT Edge.

### <a name="post-deployment-direct-method-error-code"></a>Implantação de posts: Código de erro do método direto
1. Se obter um `501 code` estado, verifique se o nome do método direto está correto. Se o nome do método e a carga útil do pedido estiverem corretos, deverá obter resultados juntamente com o código de sucesso =200. 
1. Se a carga útil do pedido for imprecisa, obterá um estado `400 code` e uma carga útil de resposta que indique código de erro e mensagem que deve ajudar a diagnosticar o problema com a sua chamada de método direto.
    * Verificar as propriedades reportadas e desejadas pode ajudá-lo a entender se as propriedades do módulo sincronizaram com a implementação. Se não o fizerem, pode reiniciar o seu dispositivo IoT Edge. 
    * Utilize o guia [de métodos diretos](direct-methods.md) para chamar alguns métodos, especialmente os simples, como o GraphTopologyList. O guia também especifica as cargas de pedido e resposta esperadas e códigos de erro. Depois de os métodos diretos simples serem bem sucedidos, pode ter a certeza de que o módulo IoT Edge live video analytics está funcionalmente OK.
        
       ![Screenshot do painel "Método Direto" para o módulo IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Se o **Especificado na implementação** e **reportado por** colunas do dispositivo indicar *sim,* pode invocar métodos diretos no vídeo live analytics no módulo IoT Edge. Selecione o módulo para ir a uma página onde pode verificar as propriedades desejadas e reportadas e invocar métodos diretos. Tenha em atenção o seguinte: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Implantação de posts: Diagnosticar registos para problemas durante a execução 

Os registos do contentor do seu módulo IoT Edge devem conter informações de diagnóstico para ajudar a depurar os seus problemas durante o tempo de funcionação do módulo. Pode [verificar os registos dos contentores para obter problemas](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) e diagnosticar o problema. 

Se executou todas as verificações anteriores e ainda está a encontrar problemas, recolha registos do dispositivo IoT Edge [com o `support bundle` comando](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) para uma análise mais aprofundada pela equipa Azure. Pode [contactar-nos](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para obter apoio e para submeter os registos recolhidos.

## <a name="common-error-resolutions"></a>Resoluções de erros comuns

O Live Video Analytics é implantado como um módulo IoT Edge no dispositivo IoT Edge, e funciona em colaboração com o agente IoT Edge e módulos hub. Alguns dos erros comuns que irá encontrar com a implementação do Live Video Analytics são causados por problemas com a infraestrutura IoT subjacente. Os erros incluem:

* [O agente IoT Edge para após cerca de um minuto](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [O agente IoT Edge não consegue aceder à imagem de um módulo (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [O módulo de agente IoT Edge relata "ficheiro config vazio" e nenhum módulo começa no dispositivo](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [O hub IoT Edge não arranca.](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start)
* [O daemon de segurança IoT Edge falha com um nome de hospedeiro inválido](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [O Live Video Analytics ou qualquer outro módulo IoT Edge personalizado não envia uma mensagem para o centro de borda com erro 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [O módulo IoT Edge é implantado com sucesso e desaparece do dispositivo](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="live-video-analytics-working-with-external-modules"></a>Vídeo ao vivo Analytics trabalhando com módulos externos

O Live Video Analytics através dos processadores de extensão de gráficos de mídia pode estender o gráfico de mídia para enviar e receber dados de outros módulos IoT Edge utilizando protocolos HTTP ou gRPC. Como [exemplo específico,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)este gráfico de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externa, como o Yolo v3 e receber resultados de análise baseados em JSON usando o protocolo HTTP . Em tal topologia, o destino para os eventos é principalmente o hub IoT. Em situações em que não se vê os eventos de inferência no centro, verifique o seguinte:

* Verifique se o centro que o gráfico de mídia está a publicar e o centro que está a examinar são os mesmos. À medida que crias várias implementações, podes acabar com vários hubs e verificar erroneamente o centro errado para eventos.
* No portal Azure, verifique se o módulo externo está implantado e em funcionamento. Na imagem de exemplo aqui, o rtspsim, o yolov3, o tinyyolov3 e o logAnalyticsAgent são módulos IoT Edge que correm externamente para o módulo LvaEdge.

    [![Screenshot que mostra o estado de funcionamento dos módulos no Azure IoT Hub. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Verifique se está a enviar eventos para o ponto final correto da URL. O contentor externo de IA expõe um URL e uma porta através da qual recebe e devolve os dados dos pedidos do POST. Este URL é especificado como uma `endpoint: url` propriedade para o processador de extensão HTTP. Como visto no [URL de topologia,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)o ponto final é definido para o parâmetro URL inferencing. Certifique-se de que o valor predefinido para o parâmetro ou o valor de entrada é exato. Pode testar para ver se está a funcionar utilizando o URL do Cliente (cURL).  

    Como exemplo, aqui está um contentor Yolo v3 que está funcionando em máquina local com um endereço IP de 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado devolvido:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Utilize **[o comando do Docker](https://docs.docker.com/engine/reference/commandline/inspect/)** para encontrar o endereço IP da máquina.
    
* Se estiver a executar uma ou mais instâncias de um gráfico que utiliza o processador de extensão de gráficos de mídia, deve utilizar o `samplingOptions` campo para gerir os quadros por segundo (fps) da taxa do feed de vídeo. 

   * Em determinadas situações, em que o CPU ou a memória da máquina de borda é altamente utilizado, pode perder certos eventos de inferência. Para resolver esta questão, deite um valor baixo para o `maximumSamplesPerSecond` imóvel no `samplingOptions` campo. Pode defini-lo para 0,5 ("maximumSamplesPerSecond": "0,5") em cada instância do gráfico e, em seguida, re-executar o caso para verificar se há eventos de inferência no centro.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Múltiplos métodos diretos em paralelo – falha no tempo limite 

Live Video Analytics on IoT Edge fornece um modelo de programação baseado em métodos diretos que lhe permite configurar várias topologias e múltiplas instâncias de gráfico. Como parte da configuração da topologia e gráfico, você invoca múltiplas chamadas de método direto no módulo IoT Edge. Se invocar estas múltiplas chamadas de método em paralelo, especialmente as que iniciam e param os gráficos, poderá experimentar uma falha de tempo limite, como: 

Método de inicialização de montagem Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync lançou exceção. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Recomendamos que *não* chame métodos diretos em paralelo. Chame-os sequencialmente (isto é, faça uma chamada de método direto apenas após a anterior estar terminada).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Recolher registos para a submissão de um bilhete de apoio

Quando os passos de resolução de problemas auto-guiados não resolverem o seu problema, vá ao portal Azure e [abra um bilhete de apoio](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Os registos podem conter informações pessoalmente identificáveis (PII) como o seu endereço IP. Todas as cópias locais dos registos serão apagadas assim que completarmos a sua análise e fecharmos o bilhete de apoio.  

Para recolher os registos relevantes que devem ser adicionados ao bilhete, siga as instruções abaixo em ordem e carrece os ficheiros de registo no painel **de detalhes** do pedido de suporte.  
1. [Configure o módulo Live Video Analytics para recolher registos verbose](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Ligue os registos de Debug](#live-video-analytics-debug-logs)
1. Reproduza o problema
1. Ligue-se à máquina virtual a partir da página **IoT Hub** no portal
    1. Feche todos os ficheiros na pasta *de depurar.*

       > [!NOTE]
       > Estes ficheiros de registo não são destinados ao autodiagnóstico. Destinam-se à equipa de engenharia do Azure para analisar os seus problemas.

       * No comando seguinte, certifique-se de que substitui **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** pela localização dos registos de depurg no dispositivo Edge que instalou anteriormente no **passo 2**.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Prenda o ficheiro *debugLogs.zip* ao bilhete de apoio.
1. Executar o [comando do pacote de suporte,](#use-the-support-bundle-command)recolher os registos e anexar ao bilhete de suporte.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Configure módulo de análise de vídeo ao vivo para recolher registos verbose
Configure o seu módulo Live Video Analytics para recolher registos Verbose definindo os `logLevel` e `logCategories` os seguintes seguintes:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Pode fazer isto em qualquer um dos dois:
* No **portal Azure,** atualizando as propriedades De Identidade De Módulo Twin do módulo de vídeo ao   [ ![ vivo, módulo de identidade twin properies. ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Ou no seu ficheiro **manifesto de implantação,** pode adicionar estas entradas no nó de propriedades do módulo Live Video Analytics

### <a name="use-the-support-bundle-command"></a>Use o comando do pacote de suporte

Quando precisa de recolher registos de um dispositivo IoT Edge, a maneira mais fácil é utilizar o `support-bundle` comando. Este comando recolhe:

- Troncos de módulos
- Gestor de segurança IoT Edge e troncos de motor de contentores
- IoT Edge verifique a saída JSON
- Informação útil do depurg

1. Executar o `support-bundle` comando  com a bandeira para especificar quanto tempo pretende que os seus registos cubram. Por exemplo, 2h receberá registos nas últimas duas horas. Pode alterar o valor desta bandeira para incluir registos para diferentes períodos.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Este comando cria um ficheiro chamado *support_bundle.zip* no diretório onde dirigia o comando. 
   
1. Prenda o ficheiro *support_bundle.zip* ao bilhete de apoio.

### <a name="live-video-analytics-debug-logs"></a>Registos de depurações de vídeo ao vivo

Para configurar o vídeo ao vivo analítico no módulo IoT Edge para gerar registos de depuração, faça o seguinte:

1. Inscreva-se no [portal Azure](https://portal.azure.com)e vá para o seu hub IoT.
1. No painel esquerdo, selecione **IoT Edge**.
1. Na lista de dispositivos, selecione o ID do dispositivo alvo.
1. Na parte superior do painel, selecione **Módulos de Conjunto**.

   ![Screenshot do botão "set Modules" no portal Azure.](media/troubleshoot-how-to/set-modules.png)

1. Na secção **IoT Edge Modules,** procure e selecione **a LvaEdge**.
1. Selecione **opções de criação de recipientes**.
1. Na secção **Binds,** adicione o seguinte comando:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Este comando liga as pastas de registo entre o dispositivo Edge e o recipiente. Se pretender recolher os registos num local diferente, utilize o seguinte comando, substituindo **$LOG_LOCATION_ON_EDGE_DEVICE** pela localização que pretende utilizar: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selecione **Atualizar**.
1. Selecione **Review + Criar**. Uma mensagem de validação bem sucedida é postada sob um banner verde.
1. Selecione **Criar**.
1. Atualizar **Módulo Identidade Twin** para apontar para o parâmetro DebugLogsDirectory, que aponta para o diretório em que os registos são recolhidos:

    a. Sob a tabela **Módulos,** selecione **LvaEdge**.  
    b. Na parte superior do painel, selecione **Módulo Identity Twin**. Abre-se um painel editável.  
    c. Na **tecla desejada,** adicione o seguinte par de teclas/valor:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Este comando liga as pastas de registo entre o dispositivo Edge e o recipiente. Se pretender recolher os registos num local diferente do dispositivo:
    > 1. Crie uma ligação para a localização do Registo de Debug na secção **Binds,** substituindo a **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** e **$DEBUG_LOG_LOCATION** com a localização desejada: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Utilize o seguinte comando, substituindo **$DEBUG_LOG_LOCATION** pela localização utilizada no passo anterior:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Selecione **Guardar**.


1. Pode parar a recolha de registos definindo o valor em **Módulo Identity Twin** para *nulo*. Volte para a página **Módulo Identidade Gémea** e atualize o seguinte parâmetro como:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Melhores práticas em torno da exploração madeireira

[A monitorização e a exploração madeireira](monitoring-logging.md) devem ajudar a compreender a taxonomia e como gerar registos que ajudem a depurar problemas com a LVA. 

Como a implementação do servidor gRPC difere entre os idiomas, não existe uma forma padrão de adicionar o início de sessão no servidor.  

Como exemplo, se construir um servidor gRPC utilizando o núcleo .NET, o serviço gRPC adiciona registos na categoria **Grpc.** Para permitir registos detalhados do gRPC, configurar os prefixos Grpc para o nível Debug no seu appsettings.jsficheiro adicionando os seguintes itens à subseção LogLevel em Registo: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Também pode configurar isto no ficheiro Startup.cs com configuração: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[O registo e diagnóstico em gRPC em .NET](https://docs.microsoft.com/aspnet/core/grpc/diagnostics?view=aspnetcore-3.1&preserve-view=true) fornece algumas orientações para a recolha de alguns registos de diagnóstico de um servidor gRPC. 

### <a name="a-failed-grpc-connection"></a>Uma ligação gRPC falhada 

Se um gráfico estiver ativo e em streaming a partir de uma câmara, a ligação será mantida pelo Live Video Analytics. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Monitorização e equilíbrio da carga de recursos da CPU e da GPU quando estes recursos se tornam estrangulamentos

O Live Video Analytics não monitoriza nem fornece qualquer monitorização de recursos de hardware. Os desenvolvedores terão de usar as soluções de monitorização dos fabricantes de hardware. No entanto, se utilizar recipientes Kubernetes, pode monitorizar o dispositivo utilizando o [painel kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC em documentos principais .NET também compartilham algumas informações valiosas sobre [as melhores práticas de desempenho](https://docs.microsoft.com/aspnet/core/grpc/performance?view=aspnetcore-3.1&preserve-view=true) e o equilíbrio de [carga.](https://docs.microsoft.com/aspnet/core/grpc/performance?view=aspnetcore-3.1#load-balancing&preserve-view=true)  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Resolução de problemas num servidor de inferência quando não recebe nenhum quadro e está a receber, um erro de protocolo "desconhecido" 

Há várias coisas que pode fazer para obter mais informações sobre o problema.  

* Inclua a categoria de registo **ediaPipeline** nas propriedades desejadas do módulo Live Video Analytics e certifique-se de que o nível de registo está definido para `Information` .  
* Para testar a conectividade da rede, pode executar o seguinte comando a partir do dispositivo de borda. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Se o comando der uma pequena sequência de texto confuso, então a telnet foi capaz de abrir uma ligação ao seu servidor de inferência e abrir um canal binário gRPC. Se não vir isto, a Telnet reportará um erro de rede. 
* No seu servidor de inferência pode ativar a sessão de registos adicionais na biblioteca gRPC. Isto pode dar informações adicionais sobre o próprio canal gRPC. Ao fazê-lo varia de acordo com a linguagem, aqui estão as instruções para [C#](https://docs.microsoft.com/aspnet/core/grpc/diagnostics?view=aspnetcore-3.1&preserve-view=true). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Recolher mais imagens do tampão do gRPC sem enviar de volta o resultado para o primeiro tampão

Como parte do contrato de transferência de dados gRPC, todas as mensagens que o Live Video Analytics envia para o servidor de inferencing gRPC devem ser reconhecidas. Não reconhecer a receção de uma moldura de imagem quebra o contrato de dados e pode resultar em situações não desejadas.  

Para utilizar o seu servidor gRPC com Live Video Analytics, a memória partilhada pode ser usada para melhor desempenho. Isto requer que utilize as capacidades de memória partilhadas do Linux expostas pela linguagem/ambiente de programação. 

1. Abra o cabo de memória partilhada Linux.
1. Ao receber uma moldura, aceda ao endereço offset dentro da memória partilhada.
1. Reconheça a conclusão do processamento do quadro para que a sua memória possa ser recuperada pela Live Video Analytics.

   > [!NOTE]
   > Se atrasar a receção da moldura ao Live Video Analytics por muito tempo, pode resultar em que a memória partilhada fique cheia e cause quedas de dados.
1. Guarde cada quadro numa estrutura de dados à sua escolha (lista, matriz e assim por diante) no servidor de inferenculação.
1. Em seguida, pode executar a sua lógica de processamento quando tiver o número desejado de molduras de imagem.
1. Volte a devolver o resultado de inferencing ao Live Video Analytics quando estiver pronto.

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem](event-based-video-recording-tutorial.md)
