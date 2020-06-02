---
title: Resolução de problemas Vídeo Analytics em IoT Edge - Azure
description: Este artigo cobre etapas de resolução de problemas para Live Video Analytics no IoT Edge.
ms.topic: how-to
ms.date: 05/24/2020
ms.openlocfilehash: c235dd27da1d370531c1668c40586d4ae479aec7
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261122"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Resolução de problemas Vídeo Analytics em IoT Edge

Este artigo cobre etapas de resolução de problemas para Live Video Analytics no IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Resolver problemas de implementação

### <a name="diagnostics"></a>Diagnóstico

Como parte da implementação do Live Video Analytics, irás criar recursos Azure, como o IoT Hub e o dispositivo IoT Edge. Como primeiro passo para diagnosticar problemas, garante sempre que a borda está devidamente configurada seguindo estas instruções:

1. [Executar o comando 'verificar'](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Verifique a sua versão IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Verifique o estado do gestor de segurança IoT Edge e seus registos](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Veja as mensagens que passam pelo hub IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Reiniciar contentores](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Verifique as regras de firewall e configuração da porta](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Questões de pré-implantação

Se a infraestrutura de borda estiver bem, então pode procurar problemas com o ficheiro manifesto de implantação. Para implementar o Live Video Analytics no módulo IoT Edge no dispositivo de borda ao lado de quaisquer outros módulos IoT, utilizará um manifesto de implantação que contenha o edge Hub, edge Agent e outros módulos com as suas propriedades. Se o JSON não estiver bem formado, pode obter um erro como abaixo: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Não conseguiu analisar json do ficheiro: ' <deployment manifest.json> para o argumento 'conteúdo', com exceção: "Dados extra: linha 101 coluna 1 (char 5325)"

Se encontrar este erro, é aconselhável verificar se o ficheiro JSON está em falta ou outros problemas com a estrutura do ficheiro. Pode utilizar um cliente como [o Notepad++ com plugin do JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online, como https://jsonformatter.curiousconcept.com/ validar a estrutura do ficheiro.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Implantação – diagnóstico com métodos diretos de gráfico de mídia 

Uma vez que o módulo Live Video Analytics no IoT Edge é implantado corretamente no dispositivo de borda, pode criar e executar o gráfico de mídia invocando [métodos diretos](direct-methods.md). Pode utilizar o portal para executar o diagnóstico do gráfico de mídia através de métodos diretos:

1. Via Portal, vá ao Hub IoT que está ligado ao seu dispositivo de borda.
    1. Uma vez na lâmina IoT Hub, procure o IoT Edge de gestão automática de dispositivos >.
    1. Clicar no IoT Edge deve apresentar uma lista de dispositivos de borda. Escolha o dispositivo que pretende diagnosticar.
         
        ![Dispositivos de borda](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Verifique se o código resposta é 200-OK. Existem vários outros códigos de resposta para o [tempo de execução IoT Edge,](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) tais como:
        1. 400 - A configuração de implantação é mal formada ou inválida.
        1. 417 - O dispositivo não tem um conjunto de configuração de implantação.
        1. 412 - A versão de esquema na configuração de implementação é inválida.
        1. 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
        1. 500 - Ocorreu um erro no tempo de execução do IoT Edge.
    1. Clicar no dispositivo também deve mostrar-lhe a lista de módulos IoT Edge esperados que foram implementados e o seu estado
    1. Se as colunas "especificadas na implementação" e "reportadas pelo dispositivo" indicarem "Sim", então pode invocar métodos diretos no módulo Live Video Analytics no módulo IoT Edge. Clique no módulo e irá levá-lo a um ecrã onde pode verificar as propriedades desejadas e reportadas e pode invocar métodos diretos. 
        1. Verificar as propriedades reportadas e desejadas ajuda-o a perceber se as propriedades do módulo se sincronizaram com a implementação. Se não o fizeram, pode reiniciar a sua vantagem. 
        1. Utilize o guia [de métodos diretos](direct-methods.md) para chamar alguns métodos, especialmente os simples como o GraphTopologyList. O guia também especifica as cargas de pedido e resposta esperadas e códigos de erro. Uma vez que os métodos simples Direct sejam bem sucedidos, pode ter a garantia de que o módulo de borda live video analytics está funcionalmente ok.
        
        ![Método direto](./media/troubleshoot-how-to/direct-method.png) 
1. Se tiver um código 501, verifique se o nome do método Direct está correto. Se o nome do método e a carga útil do pedido estiverem corretos, deverá receber resultados juntamente com o código de sucesso =200. Se a carga útil do pedido for imprecisa, obterá um estado =400 e uma carga útil de resposta que indique código de erro e mensagem que deve ajudar a diagnosticar o problema com a sua chamada de método direto. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Post deployment – diagnosticar registos para problemas durante a execução 

Os registos de contentores para o nosso módulo de borda devem ter diagnóstico<!--<todo:add link to diagnostics doc>--> informações que devem ajudar a depurar os seus problemas durante o tempo de funcionação do módulo. No entanto, pode verificar os registos dos [contentores para problemas](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) e auto-diagnóstico, no entanto, se todas as verificações acima referidas tiverem sido realizadas e ainda estiver em problemas, então recolha registos do dispositivo IoT Edge [com o comando 'pacote de suporte'](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) que pode ser analisado mais adiante pela equipa do Azure. Pode [contactar-nos](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para obter apoio e enviar os registos recolhidos.

## <a name="common-error-resolutions"></a>Resoluções de erros comuns

O Live Video Analytics é implantado como um módulo IoT Edge no dispositivo de borda e funciona em colaboração com o agente IoT Edge e módulos hub. Alguns dos erros comuns que irás enfrentar com a implementação do Live Video Analytics serão devidos aos problemas com a infraestrutura IoT subjacente. Alguns erros comuns que o agente e o hub da IoT Edge podem ter são:

1. [O agente IoT Edge para após cerca de um minuto](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [O agente IoT Edge não consegue aceder à imagem de um módulo (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [O módulo Edge Agent relata o "ficheiro config vazio" e nenhum módulo começa no dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [O hub IoT Edge não começa.](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start)
1. [O daemon de segurança IoT Edge falha com um nome de hospedeiro inválido](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Live Video Analytics ou qualquer outro módulo IoT Edge personalizado não envia uma mensagem para o Edge Hub com 404 erros](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [O módulo IoT Edge implementa-se com sucesso e desaparece do dispositivo](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Edge configurar problemas de script

Como parte da nossa documentação, fornecemos um [script configurado](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar recursos edge e cloud para começar com Live Video Analytics Edge. Nesta secção, capturámos erros que poderás enfrentar com o script e como desordá-los.

O script funciona parcialmente criando poucos recursos, mas falha com a seguinte mensagem:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Para resolver este problema:

1. Execute o seguinte comando:

    ```
    az --version
    ```
1. Certifique-se de que tem as seguintes extensões instaladas. A partir da escrita deste guia, a versão para extensões foi a seguinte:

    |||
    |---|---|
    |azure-cli   |      2.5.1*|
    |comando-módulos-nspkg         |   2.0.3|
    |núcleo  |    2.5.1*|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |Extensões:    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se alguma das extensões for mais antiga do que os números de lançamento acima, atualize a extensão para a versão mais recente utilizando o comando:

    ```
    az extension update --name <Extension name>
    ```

    Por exemplo, `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Problemas de aplicação de amostra

Como parte do nosso lançamento, fornecemos algum código de amostra .NET para obter a nossa comunidade de desenvolvedores. Nesta secção, capturámos erros que poderás enfrentar durante a execução do código de amostra e como depurar tais erros.

1. Program.cs falha com o seguinte erro na invocação do método direto:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Certifique-se de que tem as [ferramentas Azure IoT instaladas](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) no seu ambiente de código VS e a ligação à sua configuração do IoT Hub. (Ctrl+shift+P e, em seguida, escolha o método Select IoT Hub para ligar à sua subscrição e ao IoT Hub)
1. Verifique se pode invocar um método direto no módulo de borda via Vs Code (por exemplo, ligue para a GraphToplogyList com a seguinte carga útil { @apiVersion " " "1.0"}) e deverá receber a seguinte resposta. 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se o acima falhar, tente o seguinte:
    1. Vá ao pedido de comando no seu dispositivo de borda e escreva.
    
    ```
    sudo systemctl restart iotedge
    ```

    Isto reiniciará o dispositivo de borda e todos os módulos. Aguarde alguns minutos e corra o seguinte para confirmar que os módulos estão em funcionamento, antes de tentar voltar a utilizar o DirectMethod.

    ```
    sudo iotedge list
    ```
    1. Se acima também falhar, tente reiniciar o seu VM ou máquina.
    1. Se tudo falhar, por favor, execute o seguinte para obter um ficheiro ZIP com todos os [registos relevantes](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) a serem anexados a esse [bilhete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Se obter um código de resposta de erro 400, certifique-se de que a carga útil de invocação do método está bem formada de acordo com o guia [de métodos diretos.](direct-methods.md)
1. Se obter o código de estado 200, indica que o seu hub está a funcionar bem e que a implementação do módulo está correta e responsiva. O próximo passo é verificar se as configurações da aplicação estão precisas. A configuração da sua aplicação consiste em seguir campos no ficheiro appsettings.json. Verifique duas vezes se o dispositivoId e o móduloId estão corretos. Uma maneira fácil de verificar isto é através da secção de extensão Azure IoT Hub no VSCode. Os valores no ficheiro appsettings.json e na secção IoT Hub devem coincidir.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. Por fim, certifique-se de que dentro de appsettings.json forneceu a Cadeia de Ligação IoT Hub e não a Cadeia de Ligação do Dispositivo IoT Hub, uma vez que os seus formatos são [diferentes.](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/)

### <a name="live-video-analytics-working-with-external-modules"></a>Vídeo ao vivo Analytics trabalhando com módulos externos

O Live Video Analytics através do processador de extensão HTTP pode estender o gráfico de mídia para enviar e receber dados de outros módulos IoT Edge através do HTTP utilizando REST.  Como [exemplo específico,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) o gráfico de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externa como o Yolo V3 e receber de volta os resultados de análise baseados em JSON. Em tal topologia, o destino final dos eventos é principalmente o IoT Hub. Nas situações em que não veja os eventos de Inferência no Hub, verifique o seguinte:

1. Verifique se o Hub que o gráfico de mídia está a publicar para vs. aquele que está a examinar são os mesmos. Por vezes, à medida que crias várias implementações, vais acabar com vários hubs e podes estar a verificar erradamente o centro errado para eventos.
1. Verifique via VSCode se o módulo externo está implantado e em funcionamento. Na imagem de exemplo aqui, o rtspsim e o cv são módulos IoT Edge que correm externamente ao módulo LvaEdge.

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. Verifique se está a enviar eventos para o ponto final correto da URL. O contentor externo de IA expõe um URL e uma porta sobre a qual recebe e devolve os dados dos pedidos do POST. Este URL é especificado como um ponto final: propriedade url para o processador de extensão http. Como visto no [url de topologia](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) é definido para o parâmetro url inferencing. Certifique-se de que o valor predefinido para o parâmetro http://yolov3/score) ou o valor passado é exato e pode testar se está a funcionar com caracóis.  
    1. Como exemplo, o contentor yolo v3 que funciona na máquina local e o endereço IP do contentor é 172.17.0.3 (utilize um estivador para encontrar o endereço IP).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado devolvido:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Se estiver a executar uma ou múltiplas instâncias de um gráfico que utiliza o processador de extensão http, deverá ter um filtro de taxa de fotogramas antes de cada Processador de Extensão Http para gerir os quadros por segundo (fps) do feed de vídeo. Em determinadas situações em que o CPU/memória da máquina de bordas são altamente utilizados, pode perder certos eventos de Inferência. Para resolver este problema, desagreda um valor baixo para a propriedade máximafps no Filtro de Taxa de Fotograma. Pode defini-lo para 0,5 ("maximumFps": 0.5 ) em cada instância do gráfico e repetir para verificar se há eventos de Inferência no Hub.
    1. Em alternativa, também pode obter uma máquina de borda mais potente com CPU e memória mais altas.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Múltiplos métodos diretos em paralelo – falha no tempo limite 

Live Video Analytics on IoT Edge fornece um modelo de programação baseado em métodos diretos que permite a configuração de várias topologias e múltiplas instâncias de gráfico. Como parte da configuração da topologia e gráfico, irá invocar múltiplas chamadas de método direto no módulo de borda. Se invocar estas chamadas múltiplas de método, especialmente as que começam e param os gráficos, paralelamente, poderá experimentar algumas falhas no intervalo, como abaixo. 

Método de inicialização de montagem Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync lançou exceção. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Recomendamos que não chame métodos diretos de forma paralela, mas que o faça de forma sequencial, ou seja.  uma chamada de método direto apenas após o acabamento do anterior. 

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem](event-based-video-recording-tutorial.md)
