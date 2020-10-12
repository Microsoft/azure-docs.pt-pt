---
title: Diagnósticos de procuração reverso do Tecido de Serviço Azure
description: Saiba como monitorizar e diagnosticar o processamento de pedidos no proxy reverso para uma aplicação Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645468"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitore e diagnostice o processamento de pedido no proxy inverso

A partir do lançamento 5.7 do Service Fabric, estão disponíveis eventos de procuração inversa para recolha. Os eventos estão disponíveis em dois canais, um com apenas eventos de erro relacionados com a falha de processamento de pedidos no proxy inverso e segundo canal contendo eventos verbosos com entradas para pedidos bem sucedidos e falhados.

Consulte para [recolher eventos de procuração inversa](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) para permitir a recolha de eventos a partir destes canais em clusters de tecidos de serviço local e Azure.

## <a name="troubleshoot-using-diagnostics-logs"></a>Resolução de problemas usando registos de diagnóstico
Aqui estão alguns exemplos sobre como interpretar os registos comuns de falha que se podem encontrar:

1. Reverse proxy devolve código de estado de resposta 504 (Timeout).

    Um dos motivos pode dever-se ao facto de o serviço não ter respondido dentro do prazo de 100 pedidos.
   O primeiro evento abaixo regista os detalhes do pedido recebido no proxy reverso. 
   O segundo evento indica que o pedido falhou ao encaminhar para o serviço, devido a "erro interno = ERROR_WINHTTP_TIMEOUT" 

    A carga útil inclui:

   * **traceId**: Este GUID pode ser utilizado para correlacionar todos os eventos correspondentes a um único pedido. Nos dois eventos abaixo, o traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271,** implicando que pertencem ao mesmo pedido.
   * **requestUrl**: O URL (URL de procuração inversa) para o qual o pedido foi enviado.
   * **verbo:** verbo HTTP.
   * **remoteAddress**: Endereço do cliente que envia o pedido.
   * **resolvedServiceUrl**: URL de ponto final de serviço ao qual o pedido de entrada foi resolvido. 
   * **errorDetails**: Informações adicionais sobre a falha.

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

2. Reverso proxy devolve código de estado de resposta 404 (Não Encontrado). 
    
    Aqui está um evento exemplo onde o proxy reverso devolve 404, uma vez que não encontrou o ponto final de serviço correspondente.
    As entradas de juros da carga útil aqui são:
   * **processoRequestPhase**: Indica a fase durante o processamento do pedido quando ocorreu a falha, ***TryGetEndpoint*** i.e enquanto tenta buscar o ponto final de serviço para a frente. 
   * **errorDetails**: Lista os critérios de pesquisa do ponto final. Aqui pode ver que o ouvinteName especificado = **FrontEndListener,** enquanto que a lista de pontos finais de réplica contém apenas um ouvinte com o nome **OldListener**.
    
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
     Outro exemplo em que o proxy invertido pode devolver 404 Não Encontrado é: ApplicationGateway\Http parâmetro de configuração **SecureOnlyMode** é definido como verdadeiro com o proxy invertido a ouvir em **HTTPS**, no entanto todos os pontos finais da réplica não são seguros (ouvir em HTTP).
     O representante inverso devolve 404, uma vez que não encontra um ponto final a ouvir em HTTPS para encaminhar o pedido. Analisar os parâmetros da carga útil do evento ajuda a reduzir a questão:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. O pedido ao representante inverso falha com um erro de tempo limite. 
    Os registos do evento contêm um evento com os dados do pedido recebido (não mostrados aqui).
    O evento seguinte mostra que o serviço respondeu com um código de estado 404 e o proxy invertido inicia uma re-resolução. 

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
    Ao recolher todos os eventos, você vê um comboio de eventos mostrando cada determinação e tentativa avançada.
    O último evento da série mostra que o processamento de pedidos falhou com um tempo limite, juntamente com o número de tentativas de resolução bem sucedidas.
    
    > [!NOTE]
    > Recomenda-se manter a recolha de eventos verbose desativada por defeito e capacitar-se para a resolução de problemas numa base de necessidade.

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
    
    Se a recolha estiver ativada apenas para eventos críticos/erros, vê-se um evento com detalhes sobre o tempo limite e o número de tentativas de resolução. 
    
    Os serviços que pretendam enviar um código de estado 404 de volta ao utilizador, devem adicionar um cabeçalho "X-ServiceFabric" na resposta. Após a adição do cabeçalho à resposta, o representante invertido remete o código de estado para o cliente.  

4. Casos em que o cliente tenha desligado o pedido.

    O evento seguinte é registado quando o proxy invertido está a encaminhar a resposta para o cliente, mas o cliente desliga:

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
5. Proxy reverso devolve 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST erro é devolvido se o regime URI não for especificado para o ponto final de serviço no manifesto de serviço.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver o problema, especifique o esquema URI no manifesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Os eventos relacionados com o processamento de pedidos de websocket não estão atualmente registados. Isto será adicionado no próximo lançamento.

## <a name="next-steps"></a>Passos seguintes
* [Agregação e recolha de eventos utilizando o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para permitir a recolha de registos em clusters Azure.
* Para ver eventos de Tecido de Serviço no Estúdio Visual, consulte [monitor e diagnostice localmente.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* Consulte o [proxy reverso Configure para ligar para serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) para amostras de modelo do Azure Resource Manager para configurar um representante inverso seguro com as diferentes opções de validação de certificado de serviço.
* Leia [o Serviço De Serviços revertendo proxy](service-fabric-reverseproxy.md) para saber mais.
