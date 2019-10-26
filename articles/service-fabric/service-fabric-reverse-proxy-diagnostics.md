---
title: Diagnóstico de proxy reverso do Azure Service Fabric | Microsoft Docs
description: Saiba como monitorar e diagnosticar o processamento de solicitações no proxy reverso.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 6074b799e992371d41de050f68690e450f008789
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933969"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorar e diagnosticar o processamento de solicitações no proxy reverso

A partir da versão 5,7 do Service Fabric, os eventos de proxy reverso estão disponíveis para coleta. Os eventos estão disponíveis em dois canais, um com apenas eventos de erro relacionados à falha de processamento de solicitação no proxy reverso e no segundo canal que contém eventos detalhados com entradas para solicitações bem-sucedidas e com falha.

Consulte [coletar eventos de proxy reverso](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) para habilitar a coleta de eventos desses canais em clusters locais e do Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Solucionar problemas usando logs de diagnóstico
Aqui estão alguns exemplos de como interpretar os logs de falha comuns que um pode encontrar:

1. O proxy reverso retorna o código de status de resposta 504 (timeout).

    Um motivo pode ser devido à falha do serviço de responder dentro do período de tempo limite da solicitação.
   O primeiro evento abaixo registra em log os detalhes da solicitação recebida no proxy reverso. 
   O segundo evento indica que a solicitação falhou ao encaminhar para o serviço, devido a "erro interno = ERROR_WINHTTP_TIMEOUT" 

    A carga inclui:

   * **TraceID**: esse GUID pode ser usado para correlacionar todos os eventos correspondentes a uma única solicitação. Nos dois eventos abaixo, TraceID = **2f87b722-e254-4ac2-a802-fd315c1a0271**, implicando que eles pertencem à mesma solicitação.
   * **requestUrl**: a URL (URL de proxy reverso) para a qual a solicitação foi enviada.
   * **verbo: verbo**http.
   * **RemoteAddress**: endereço do cliente que está enviando a solicitação.
   * **resolvedServiceUrl**: URL de ponto de extremidade de serviço para a qual a solicitação de entrada foi resolvida. 
   * **errorDetails**: informações adicionais sobre a falha.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. O proxy reverso retorna o código de status de resposta 404 (não encontrado). 
    
    Veja um evento de exemplo em que o proxy reverso retorna 404, já que ele não encontrou o ponto de extremidade de serviço correspondente.
    As entradas de conteúdo de interesse aqui são:
   * **processRequestPhase**: indica a fase durante o processamento da solicitação quando a falha ocorreu, ***TryGetEndpoint*** ou seja ao tentar buscar o ponto de extremidade de serviço para encaminhar. 
   * **errorDetails**: lista os critérios de pesquisa do ponto de extremidade. Aqui você pode ver que listenername especificado = **FrontEndListener** , enquanto que a lista de pontos de extremidade de réplica contém apenas um ouvinte com o nome **OldListener**.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Outro exemplo em que o proxy reverso pode retornar 404 não encontrado é: o parâmetro de configuração ApplicationGateway\Http **SecureOnlyMode** está definido como true com o proxy reverso escutando em **https**, mas todos os pontos de extremidade de réplica não são seguros ( escutando em HTTP).
     O proxy reverso retorna 404, pois não consegue encontrar um ponto de extremidade ouvindo em HTTPS para encaminhar a solicitação. A análise dos parâmetros no conteúdo do evento ajuda a restringir o problema:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. A solicitação para o proxy reverso falha com um erro de tempo limite. 
    Os logs de eventos contêm um evento com os detalhes da solicitação recebida (não mostrado aqui).
    O próximo evento mostra que o serviço respondeu com um código de status 404 e o proxy reverso inicia uma reresolução. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Ao coletar todos os eventos, você verá um treinamento de eventos mostrando todas as tentativas de resolução e encaminhamento.
    O último evento da série mostra que o processamento da solicitação falhou com um tempo limite, junto com o número de tentativas de resolução bem-sucedidas.
    
    > [!NOTE]
    > É recomendável manter a coleta de eventos do canal detalhado desabilitada por padrão e habilitá-la para solução de problemas de acordo com a necessidade.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Se a coleção estiver habilitada somente para eventos críticos/de erro, você verá um evento com detalhes sobre o tempo limite e o número de tentativas de resolução. 
    
    Os serviços que pretendem enviar um código de status 404 de volta para o usuário devem adicionar um cabeçalho "X-perfabric" na resposta. Depois que o cabeçalho é adicionado à resposta, o proxy reverso encaminha o código de status de volta para o cliente.  

4. Casos em que o cliente desconectou a solicitação.

    O evento a seguir é registrado quando o proxy reverso está encaminhando a resposta para o cliente, mas o cliente se desconecta:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. O proxy reverso retorna 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    O erro FABRIC_E_SERVICE_DOES_NOT_EXIST será retornado se o esquema de URI não for especificado para o ponto de extremidade de serviço no manifesto do serviço.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver o problema, especifique o esquema de URI no manifesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Os eventos relacionados ao processamento de solicitações WebSocket não estão registrados no momento. Isso será adicionado na próxima versão.

## <a name="next-steps"></a>Passos seguintes
* [Agregação e coleta de eventos usando o Windows diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para habilitar a coleta de log em clusters do Azure.
* Para exibir Service Fabric eventos no Visual Studio, consulte [monitorar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Consulte [Configurar o proxy reverso para se conectar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) para exemplos de modelo de Azure Resource Manager para configurar o proxy reverso seguro com as diferentes opções de validação de certificado de serviço.
* Leia [Service Fabric proxy reverso](service-fabric-reverseproxy.md) para saber mais.
