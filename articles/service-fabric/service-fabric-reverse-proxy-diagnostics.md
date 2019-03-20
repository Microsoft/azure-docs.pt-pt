---
title: Diagnóstico de proxy inverso do Azure Service Fabric | Documentos da Microsoft
description: Saiba como monitorizar e diagnosticar o processamento da solicitação, o proxy inverso.
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
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121490"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorizar e diagnosticar o processamento da solicitação, o proxy inverso

A partir da versão 5.7 do Service Fabric, estão disponíveis para a recolha de eventos de proxy inverso. Os eventos estão disponíveis em dois canais, uma com apenas eventos de erro relacionados com a falha de processamento de pedidos com o proxy inverso e um segundo canal que contém eventos verbosos com entradas para pedidos com êxito ou falhadas.

Consulte a [recolher eventos de proxy inverso](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) para ativar a recolha de eventos destes canais no local e os clusters do Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Resolver problemas com registos de diagnóstico
Aqui estão alguns exemplos sobre como interpretar os registos de falha comuns que um pode encontrar:

1. Proxy inverso devolverá o código de estado de resposta 504 (tempo limite).

    Uma das razões pode dever-o a conseguir responder dentro do período de tempo limite de pedido de serviço.
   O primeiro evento abaixo os detalhes do pedido recebido, o proxy inverso de registos. 
   O segundo evento indica que o pedido falhou ao encaminhamento ao serviço, devido a "Erro interno = ERROR_WINHTTP_TIMEOUT" 

    O payload inclui:

   * **traceId**: Este GUID pode ser utilizado para correlacionar a todos os eventos correspondentes a um único pedido. Na abaixo dois eventos, o traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, implicando a que pertencem à mesma solicitação.
   * **requestUrl**: O URL (URL de proxy inverso) para o qual o pedido foi enviado.
   * **verb**: Verbo HTTP.
   * **remoteAddress**: Endereço do cliente enviar o pedido.
   * **resolvedServiceUrl**: URL de ponto final de serviço ao qual a solicitação de entrada foi resolvida. 
   * **errorDetails**: Obter informações adicionais sobre a falha.

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

2. Proxy inverso devolve o código de estado de resposta 404 (não encontrado). 
    
    Aqui está um evento de exemplo em que o proxy inverso devolve 404, uma vez que ele não foi possível encontrar o ponto de extremidade de serviço correspondente.
    As entradas de payload de interesse aqui são:
   * **processRequestPhase**: Indica a fase de durante o processamento de solicitação quando a falha ocorreu, ***TryGetEndpoint*** como ao tentar obter o ponto final de serviço para reencaminhar para. 
   * **errorDetails**: Lista os critérios de pesquisa de ponto final. Aqui pode ver que o listenerName especificado = **FrontEndListener** ao passo que a lista de ponto final de réplica contém apenas um serviço de escuta com o nome **OldListener**.
    
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
     Outro exemplo em que o proxy reverso pode devolver 404 não encontrado é: Parâmetro de configuração de ApplicationGateway\Http **SecureOnlyMode** está definido como verdadeiro com o proxy inverso a escutar **HTTPS**, no entanto, todos os pontos de extremidade de réplica são inseguros (escuta de HTTP).
     Inverter devolve de proxy 404, uma vez que ele não é possível localizar um ponto de extremidade escutando HTTPS para encaminhar a solicitação. Analisar os parâmetros no caso de payload ajuda a limitar o problema:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Pedido para o proxy inverso falha com um erro de tempo limite. 
    Os registos de eventos contêm um evento com os detalhes de pedido recebido (não mostrados aqui).
    O próximo evento mostra que o serviço respondeu com um código de 404 estado e proxy inverso inicia uma voltar a resolver. 

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
    Aquando da recolha de todos os eventos, verá um comboio de eventos que mostra cada resolve e a tentativa de encaminhamento.
    O último evento na série mostra que o processamento do pedido falhou com um tempo limite, juntamente com o número de tentativas de resolver com êxito.
    
    > [!NOTE]
    > Recomenda-se mantenha a recolha de eventos de canal verboso desativada por predefinição e habilitá-lo para resolução de problemas de forma necessidade.

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
    
    Se a coleção estiver ativada para apenas os eventos de erro/crítico, verá um evento com detalhes sobre o tempo limite e o número de tentativas de resolução. 
    
    Serviços que pretendem enviar um código de 404 estado ao usuário, deve adicionar um cabeçalho "X-ServiceFabric" na resposta. Depois do cabeçalho é adicionado à resposta, o proxy inverso reencaminha o código de estado para o cliente.  

4. Casos em que o cliente desligou o pedido.

    Seguinte evento é registado quando o proxy inverso está a reencaminhar a resposta ao cliente, mas o cliente desligar:

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
5. Proxy inverso devolve 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    Erro FABRIC_E_SERVICE_DOES_NOT_EXIST é devolvido se o esquema URI não está especificado para o ponto final de serviço no manifesto do serviço.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver o problema, especifique o esquema URI no manifesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Eventos relacionados com o processamento da solicitação websocket não são atualmente registados. Isso será adicionado na próxima versão.

## <a name="next-steps"></a>Passos Seguintes
* [Agregação de eventos e coleções com o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para ativar a recolha de registos em clusters do Azure.
* Para ver eventos do Service Fabric no Visual Studio, consulte [monitorizar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Consulte a [configurar o proxy inverso para ligar a serviços seguros](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) para o Azure Resource Manager exemplos de modelos para configurar o secure proxy inverso, com o certificado de serviço diferentes opções de validação.
* Leia [proxy inverso do Service Fabric](service-fabric-reverseproxy.md) para saber mais.
