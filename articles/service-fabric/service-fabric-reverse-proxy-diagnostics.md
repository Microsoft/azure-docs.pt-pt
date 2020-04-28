---
title: Azure Service Fabric reverter diagnósticos proxy
description: Saiba como monitorizar e diagnosticar o processamento de pedidos no proxy inverso para uma aplicação Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645468"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorizar e diagnosticar processamento de pedidos no proxy inverso

A partir do lançamento 5.7 do Service Fabric, estão disponíveis eventos de procuração inversa para recolha. Os eventos estão disponíveis em dois canais, um com apenas eventos de erro relacionados com a falha de processamento de pedidos no proxy invertido e segundo canal contendo eventos verbosos com entradas para pedidos bem sucedidos e falhados.

Consulte a [Collect eventos de procuração reversa](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) para permitir a recolha de eventos destes canais em clusters locais e azure service fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Resolução de problemas usando registos de diagnóstico
Aqui estão alguns exemplos sobre como interpretar os registos comuns de falha que se podem encontrar:

1. O código de estado de resposta de retorna ao proxy invertido 504 (Timeout).

    Uma das razões pode dever-se ao facto de o serviço não ter respondido no prazo de saída do pedido.
   O primeiro evento abaixo regista os detalhes do pedido recebido no proxy inverso. 
   O segundo evento indica que o pedido falhou durante o encaminhamento para o serviço, devido a "erro interno = ERROR_WINHTTP_TIMEOUT" 

    A carga útil inclui:

   * **traceId**: Este GUID pode ser utilizado para correlacionar todos os eventos correspondentes a um único pedido. Nos dois eventos abaixo, o traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271,** o que implica que pertencem ao mesmo pedido.
   * **pedidoUrl**: O URL (URL de procuração inversa) para o qual o pedido foi enviado.
   * **verbo**: VERbo HTTP.
   * **endereço remoto**: Endereço do cliente que envia o pedido.
   * **resolvidoServiceUrl**: URL final do ponto final do serviço para o qual o pedido de entrada foi resolvido. 
   * **erroDetalhes**: Informações adicionais sobre a falha.

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

2. O proxy inverso devolve o código de estado de resposta 404 (não encontrado). 
    
    Aqui está um evento de exemplo onde o proxy invertido devolve 404 uma vez que não encontrou o ponto final do serviço correspondente.
    As entradas de juros da carga útil aqui são:
   * **processRequestPhase**: Indica a fase durante o processamento de pedidos quando ocorreu a falha, ***TryGetEndpoint*** i.e enquanto tenta obter o ponto final do serviço para avançar. 
   * **erroDetalhes**: Lista os critérios de pesquisa do ponto final. Aqui pode ver que o ouvinteNome especificado = **FrontEndListener,** enquanto a lista de pontofinal da réplica contém apenas um ouvinte com o nome **OldListener**.
    
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
     Outro exemplo em que o proxy inverso pode devolver 404 Não Encontrado é: ApplicationGateway\Http o parâmetro de configuração **SecureOnlyMode** está definido para ser verdade com a escuta de procuração inversa em **HTTPS**, no entanto todos os pontos finais da réplica não estão seguros (ouvindo em HTTP).
     O proxy inverso devolve 404, uma vez que não encontra um ponto final a ouvir em HTTPS para encaminhar o pedido. Analisar os parâmetros no evento a carga útil ajuda a reduzir a questão:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. O pedido ao proxy invertido falha com um erro de tempo. 
    Os registos do evento contêm um evento com os detalhes do pedido recebidos (não mostrados aqui).
    O evento seguinte mostra que o serviço respondeu com um código de estado 404 e o representante invertido inicia uma redeterminação. 

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
    Ao recolher todos os eventos, você vê uma série de eventos mostrando cada determinação e tentativa para a frente.
    O último evento da série mostra que o processamento de pedidos falhou com um tempo de paragem, juntamente com o número de tentativas de resolução bem sucedidas.
    
    > [!NOTE]
    > Recomenda-se manter a recolha de eventos do canal verboso desativada por padrão e capacitá-la para resolução de problemas numa base de necessidade.

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
    
    Se a recolha estiver ativada apenas para eventos críticos/erros, você vê um evento com detalhes sobre o tempo de paragem e o número de tentativas de resolução. 
    
    Os serviços que pretendem enviar um código de estado 404 de volta ao utilizador, devem adicionar um cabeçalho "X-ServiceFabric" na resposta. Depois de adicionado o cabeçalho à resposta, o proxy inverso reencaminha o código de estado de volta para o cliente.  

4. Casos em que o cliente desligou o pedido.

    O seguinte evento é registado quando o proxy inverso está a encaminhar a resposta ao cliente, mas o cliente desliga:

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
5. Procuração Inversa devolve 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    FABRIC_E_SERVICE_DOES_NOT_EXIST erro é devolvido se o regime URI não for especificado para o ponto final do serviço no manifesto de serviço.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver o problema, especifique o regime URI no manifesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Os eventos relacionados com o processamento de pedidos de websocket não estão atualmente registados. Isto será adicionado no próximo lançamento.

## <a name="next-steps"></a>Passos seguintes
* Agregação e recolha de [eventos utilizando o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para permitir a recolha de log em clusters Azure.
* Para ver eventos de Tecido de Serviço em Estúdio Visual, consulte [Monitor e diagnostice localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Consulte o [Proxy inverso configure para ligar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) para amostras de modelo do Gestor de Recursos Azure para configurar proxy inverso seguro com as diferentes opções de validação de certificadode serviço.
* Leia [o serviço Fabric revertê-lo](service-fabric-reverseproxy.md) para saber mais.
