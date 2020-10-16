---
title: Resolução de problemas Vídeo Analytics em IoT Edge - Azure
description: Este artigo cobre etapas de resolução de problemas para Live Video Analytics no IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823203"
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

Se a infraestrutura de borda estiver bem, pode procurar problemas com o ficheiro manifesto de implantação. Para implementar o módulo Live Video Analytics no módulo IoT Edge no dispositivo IoT Edge ao lado de quaisquer outros módulos IoT, utilize um manifesto de implantação que contenha o hub IoT Edge, o agente IoT Edge e outros módulos e suas propriedades. Se o código JSON não estiver bem formado, poderá receber o seguinte erro: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Não analisou json do ficheiro: ' <deployment manifest.json> para o argumento 'conteúdo', com exceção: "Dados extra: linha 101 coluna 1 (char 5325)"

Se encontrar este erro, recomendamos que verifique se o JSON está em falta ou outros problemas com a estrutura do ficheiro. Para validar a estrutura do ficheiro, pode utilizar um cliente como o [Notepad++ com plug-in do Visualizador JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) ou uma ferramenta online como o [JSON Formatter & Validador](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Durante a implementação: Diagnosticar com métodos diretos de gráfico de mídia 

Depois de o módulo Live Video Analytics no IoT Edge ser implantado corretamente no dispositivo IoT Edge, pode criar e executar o gráfico de mídia invocando [métodos diretos](direct-methods.md). Pode utilizar o portal Azure para executar um diagnóstico do gráfico mediático através de métodos diretos:

1. No portal Azure, vá ao hub IoT que está ligado ao seu dispositivo IoT Edge.

1. Procure a **gestão automática do dispositivo**e, em seguida, selecione **IoT Edge**.  

1. Na lista de dispositivos Edge, selecione o dispositivo que pretende diagnosticar.  
         
    ![Screenshot do portal Azure mostrando uma lista de dispositivos Edge](./media/troubleshoot-how-to/lva-sample-device.png)

1. Verifique se o código de resposta é *200-OK*. Outros códigos de resposta para o [tempo de execução IoT Edge](../../iot-edge/iot-edge-runtime.md) incluem:
    * 400 - A configuração de implantação é mal formada ou inválida.
    * 417 - O dispositivo não tem um conjunto de configuração de implantação.
    * 412 - A versão de esquema na configuração de implementação é inválida.
    * 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
    * 500 - Ocorreu um erro no tempo de execução do IoT Edge.

1. Se tiver um código 501, verifique se o nome do método direto está correto. Se o nome do método e a carga útil do pedido estiverem corretos, deverá obter resultados juntamente com o código de sucesso =200. Se a carga útil do pedido for imprecisa, obterá um estado =400 e uma carga útil de resposta que indique código de erro e mensagem que deve ajudar a diagnosticar o problema com a sua chamada de método direto.
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

### <a name="edge-setup-script-issues"></a>Problemas de script de configuração de borda

Como parte da nossa documentação, fornecemos um [script de configuração](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar recursos de borda e nuvem e começar com Live Video Analytics Edge. Esta secção apresenta alguns erros de script que poderá encontrar, juntamente com soluções para depurá-los.

Problema: O script executa, em parte criando poucos recursos, mas falha com a seguinte mensagem:

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
1. Certifique-se de que tem as seguintes extensões instaladas. A partir da publicação deste artigo, as extensões e as suas versões são:

    | Extensão | Versão |
    |---|---|
    |azure-cli   |      2.5.1*|
    |comando-módulos-nspkg         |   2.0.3|
    |núcleo  |    2.5.1*|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Se tiver uma extensão instalada cuja versão seja mais cedo do que o número de desbloqueio aqui indicado, atualize a extensão utilizando o seguinte comando:

    ```
    az extension update --name <Extension name>
    ```

    Por exemplo, pode `az extension update --name azure-iot` correr.

### <a name="sample-app-issues"></a>Problemas de aplicação de amostra

Como parte do nosso lançamento, fornecemos um código de amostra .NET para ajudar a obter a nossa comunidade de desenvolvedores. Esta secção apresenta alguns erros que poderá encontrar quando executar o código de amostra, juntamente com soluções para depurá-los.

Problema: Program.cs falha com o seguinte erro na invocação do método direto:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Certifique-se de que tem [ferramentas Azure IoT instaladas](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) no seu ambiente visual Studio Code e que configura a ligação ao seu hub IoT. Para tal, selecione Ctrl+Shift+P e, em seguida, escolha **o método Select IoT Hub**.

1. Verifique se pode invocar um método direto no módulo IoT Edge via Visual Studio Code. Por exemplo, ligue para o GraphTopologyList com a seguinte carga útil &nbsp; { @apiVersion " : "1.0"}. Deverá receber a seguinte resposta: 

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

    ![Screenshot da resposta no Código do Estúdio Visual.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Se a solução anterior falhar, tente o seguinte:

    a. Vá ao pedido de comando no seu dispositivo IoT Edge e execute o seguinte comando:
    
      ```
      sudo systemctl restart iotedge
      ```

      Este comando reinicia o dispositivo IoT Edge e todos os módulos. Aguarde alguns minutos e, em seguida, antes de tentar usar o método direto novamente, confirme que os módulos estão funcionando com o seguinte comando:

      ```
      sudo iotedge list
      ```

    b. Se a abordagem anterior também falhar, tente reiniciar a sua máquina virtual ou computador.

    c. Se todas as abordagens falharem, executar o seguinte comando para obter um ficheiro com fecho com todos os [registos relevantes](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command), e anexá-lo a um [bilhete de apoio](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Se obter um código de resposta de erro *400,* certifique-se de que a carga útil de invocação do seu método está bem formada, de acordo com o guia [de métodos Diretos.](direct-methods.md)
1. Se obter um código de estado *200,* indica que o seu hub está a funcionar bem e que a implementação do módulo está correta e responsiva. 

1. Verifique se a configuração da aplicação está correta. A configuração da sua aplicação consiste nos seguintes campos no *appsettings.jsficheiro.* Verifique duas vezes se o dispositivoId e o móduloId estão corretos. Uma maneira fácil de verificar é indo para a secção de extensão Azure IoT Hub em Visual Studio Code. Os valores do *appsettings.jsno* ficheiro e a secção IoT Hub deve coincidir.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. Na *appsettings.jsno* ficheiro, certifique-se de que forneceu a cadeia de ligação IoT Hub e *não* a cadeia de ligação do dispositivo IoT Hub, porque os [formatos de cadeia de ligação são diferentes](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Vídeo ao vivo Analytics trabalhando com módulos externos

O Live Video Analytics através do processador de extensão HTTP pode estender o gráfico de mídia para enviar e receber dados de outros módulos IoT Edge em HTTP utilizando REST. Como [exemplo específico,](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)o gráfico de mídia pode enviar quadros de vídeo como imagens para um módulo de inferência externa, como o Yolo v3 e receber resultados de análise baseados em JSON. Em tal topologia, o destino para os eventos é principalmente o hub IoT. Em situações em que não se vê os eventos de inferência no centro, verifique o seguinte:

* Verifique se o centro que o gráfico de mídia está a publicar e o centro que está a examinar são os mesmos. À medida que crias várias implementações, podes acabar com vários hubs e verificar erroneamente o centro errado para eventos.
* No Código do Estúdio Visual, verifique se o módulo externo está implantado e em funcionamento. Na imagem de exemplo aqui, o rtspsim e o cv são módulos IoT Edge que correm fora do módulo LvaEdge.

    ![Screenshot que mostra o estado de funcionamento dos módulos no Azure IoT Hub.](./media/troubleshoot-how-to/iot-hub.png)

* Verifique se está a enviar eventos para o ponto final correto da URL. O contentor externo de IA expõe um URL e uma porta através da qual recebe e devolve os dados dos pedidos do POST. Este URL é especificado como uma `endpoint: url` propriedade para o processador de extensão HTTP. Como visto no [URL de topologia,](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)o ponto final é definido para o parâmetro URL inferencing. Certifique-se de que o valor predefinido para o parâmetro ou o valor de entrada é exato. Pode testar para ver se está a funcionar utilizando o URL do Cliente (cURL).  

    Como exemplo, aqui está um contentor Yolo v3 que está funcionando em máquina local com um endereço IP de 172.17.0.3. Utilize o Docker para encontrar o endereço IP.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Resultado devolvido:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Se estiver a executar uma ou mais instâncias de um gráfico que utilize o processador de extensão HTTP, deve ter um filtro de taxa de fotogramas antes de cada processador de extensão HTTP para gerir os quadros por segundo (fps) da taxa do feed de vídeo. 

   Em determinadas situações, em que o CPU ou a memória da máquina de borda é altamente utilizado, pode perder certos eventos de inferência. Para resolver este problema, deite um valor baixo para a propriedade máximafps no filtro de taxa de fotograma. Pode defini-lo para 0,5 ("maximumFps": 0.5 ) em cada instância do gráfico e, em seguida, reexecur o caso para verificar se há eventos de inferência no centro.

   Em alternativa, pode obter uma máquina de borda mais potente com CPU e memória mais elevadas.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Múltiplos métodos diretos em paralelo – falha no tempo limite 

Live Video Analytics on IoT Edge fornece um modelo de programação baseado em métodos diretos que lhe permite configurar várias topologias e múltiplas instâncias de gráfico. Como parte da configuração da topologia e gráfico, você invoca múltiplas chamadas de método direto no módulo IoT Edge. Se invocar estas múltiplas chamadas de método em paralelo, especialmente as que iniciam e param os gráficos, poderá experimentar uma falha de tempo limite, como: 

Método de inicialização de montagem Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync lançou exceção. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Recomendamos que *não* chame métodos diretos em paralelo. Chame-os sequencialmente (isto é, faça uma chamada de método direto apenas após a anterior estar terminada).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Recolher registos para a submissão de um bilhete de apoio

Quando os passos de resolução de problemas auto-guiados não resolverem o seu problema, vá ao portal Azure e [abra um bilhete de apoio](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Os registos podem conter informações pessoalmente identificáveis (PII) como o seu endereço IP. Todas as cópias locais dos registos serão apagadas assim que completarmos a sua análise e fecharmos o bilhete de apoio.  

Para recolher os registos relevantes que devem ser adicionados ao bilhete, siga as instruções nas secções seguintes. Pode fazer o upload dos ficheiros de registo no painel **de detalhes** do pedido de suporte.

### <a name="use-the-support-bundle-command"></a>Use o comando do pacote de suporte

Quando precisa de recolher registos de um dispositivo IoT Edge, a maneira mais fácil é utilizar o `support-bundle` comando. Este comando recolhe:

- Troncos de módulos
- Gestor de segurança IoT Edge e troncos de motor de contentores
- Iotedge verificar saída JSON
- Informação útil do depurg

1. Executar o `support-bundle` comando *--since* com a bandeira para especificar quanto tempo pretende que os seus registos cubram. Por exemplo, 2h receberá registos nas últimas duas horas. Pode alterar o valor desta bandeira para incluir registos para diferentes períodos.

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

1. Reproduza o problema.
1. Ligue-se à máquina virtual a partir da página **IoT Hub** no portal.
1. Feche todos os ficheiros na pasta *de depurar.*

   > [!NOTE]
   > Estes ficheiros de registo não são destinados ao autodiagnóstico. Destinam-se à equipa de engenharia do Azure para analisar os seus problemas.

   a. No comando seguinte, certifique-se de que substitui **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** pela localização dos registos de depurg no dispositivo Edge que configura anteriormente.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Prenda o ficheiro *debugLogs.zip* ao bilhete de apoio.

1. Pode parar a recolha de registos definindo o valor em **Módulo Identity Twin** para *nulo*. Volte para a página **Módulo Identidade Gémea** e atualize o seguinte parâmetro como:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Gravação de vídeo baseada em eventos para nuvem e reprodução da nuvem](event-based-video-recording-tutorial.md)
