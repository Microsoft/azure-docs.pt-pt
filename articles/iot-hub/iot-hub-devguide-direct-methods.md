---
title: Entender os métodos diretos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – Use métodos diretos para invocar o código em seus dispositivos de um aplicativo de serviço.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: dcbc03257b8bfeacda700f60f2724f2d02ec147d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048274"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Entender e invocar métodos diretos do Hub IoT

O Hub IoT oferece a capacidade de invocar métodos diretos em dispositivos da nuvem. Os métodos diretos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada HTTP em que eles são bem-sucedidos ou falham imediatamente (após um tempo limite especificado pelo usuário). Essa abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método de dispositivo tem como alvo um único dispositivo. [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) mostra como fornecer uma forma de invocar métodos diretos em vários dispositivos, e agendar a invocação do método para dispositivos desligados.

Qualquer pessoa com permissões de **ligação** de serviço no IoT Hub pode invocar um método num dispositivo.

Os métodos diretos seguem um padrão de solicitação-resposta e destinam-se a comunicações que exigem confirmação imediata de seus resultados. Por exemplo, controle interativo do dispositivo, como ativar um ventilador.

Consulte a orientação de [comunicação Cloud-to-device,](iot-hub-devguide-c2d-guidance.md) em caso de dúvida entre a utilização de propriedades desejadas, métodos diretos ou mensagens cloud-to-device.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Os métodos diretos são implementados no dispositivo e podem exigir zero ou mais entradas na carga do método para instanciar corretamente. Invoca um método direto através de um URI virado para o serviço (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos através de um tópico MQTT específico do dispositivo (`$iothub/methods/POST/{method name}/`) ou através de ligações AMQP (as propriedades de aplicação `IoThub-methodname` e `IoThub-status`). 

> [!NOTE]
> Quando invoca um método direto num dispositivo, os nomes e valores de propriedade só podem conter alfanumérico imprimível US-ASCII, exceto qualquer um no seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Os métodos diretos são síncronos e são bem-sucedidos ou falham após o período de tempo limite (padrão: 30 segundos, configurável até 300 segundos). Os métodos diretos são úteis em cenários interativos em que você deseja que um dispositivo aja se e somente se o dispositivo estiver online e recebendo comandos. Por exemplo, ativar uma luz de um telefone. Nesses cenários, você deseja ver um êxito ou uma falha imediata para que o serviço de nuvem possa agir no resultado assim que possível. O dispositivo pode retornar um corpo de mensagem como resultado do método, mas não é necessário para o método fazer isso. Não há nenhuma garantia de ordenação ou de qualquer semântica de simultaneidade em chamadas de método.

Os métodos diretos são somente HTTPS do lado da nuvem e MQTT ou AMQP do lado do dispositivo.

A carga para solicitações de método e respostas é um documento JSON de até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto de um aplicativo de back-end

Agora, invoque um método direto de um aplicativo de back-end.

### <a name="method-invocation"></a>Invocação de método

Invocações de método direto em um dispositivo são chamadas HTTPS que são feitas dos seguintes itens:

* O *pedido URI* específico do dispositivo juntamente com a versão [API:](/rest/api/iothub/service/invokedevicemethod)

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* O *método* POST

* *Cabeçalhos* que contenham a autorização, solicitar id, tipo de conteúdo e codificação de conteúdo.

* Um *corpo* JSON transparente no seguinte formato:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

O valor fornecido como `responseTimeoutInSeconds` no pedido é o tempo que o serviço IoT Hub deve aguardar para a conclusão de uma execução de método direto num dispositivo. Estabeleça este prazo para ser pelo menos o tempo esperado de execução de um método direto por um dispositivo. Se o tempo não for fornecido, o valor padrão de 30 segundos é utilizado. Os valores mínimos e máximos para `responseTimeoutInSeconds` são de 5 e 300 segundos, respectivamente.

O valor fornecido como `connectTimeoutInSeconds` no pedido é o tempo após a invocação de um método direto que o serviço IoT Hub deve aguardar que um dispositivo desligado entre online. O valor predefinido é 0, o que significa que os dispositivos já devem estar online após a invocação de um método direto. O valor máximo para `connectTimeoutInSeconds` é de 300 segundos.


#### <a name="example"></a>Exemplo

Veja abaixo um exemplo de sosseado usando `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Resposta

O aplicativo de back-end recebe uma resposta composta dos seguintes itens:

* Código de *estado HTTP:*
  * 200 indica execução bem sucedida do método direto;
  * 404 indica que ou o ID do dispositivo é inválido ou que o dispositivo não estava on-line após a invocação de um método direto e para `connectTimeoutInSeconds` posteriormente (utilize uma mensagem de erro acompanhada para compreender a causa raiz);
  * 504 indica tempo de saída causado pelo dispositivo que não responde a uma chamada de método direto dentro `responseTimeoutInSeconds`.

* *Cabeçalhos* que contenham o ETag, solicitar ID, tipo de conteúdo e codificação de conteúdo.

* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Tanto `status` como `body` são fornecidos pelo dispositivo e utilizados para responder com o código de estado e/ou descrição do próprio dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos de IoT Edge

Invocar métodos diretos utilizando um id de módulo é suportado no [IoT C# Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Para o efeito, utilize o método `ServiceClient.InvokeDeviceMethodAsync()` e passe no `deviceId` e `moduleId` como parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Manipular um método direto em um dispositivo

Vejamos como tratar um método direto em um dispositivo IoT.

### <a name="mqtt"></a>MQTT

A seção a seguir é para o protocolo MQTT.

#### <a name="method-invocation"></a>Invocação de método

Os dispositivos recebem pedidos de método direto sobre o tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. O número de assinaturas por dispositivo é limitado a 5. Portanto, é recomendável não assinar cada método direto individualmente. Em vez disso, considere subscrever `$iothub/methods/POST/#` e, em seguida, filtrar as mensagens entregues com base nos nomes dos métodos pretendidos.

O corpo que o dispositivo recebe está no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

As solicitações de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia respostas para `$iothub/methods/res/{status}/?$rid={request id}`, onde:

* A propriedade `status` é o estado fornecido pelo dispositivo de execução do método.

* A propriedade `$rid` é o pedido de identificação do método invocação recebida do IoT Hub.

O corpo é definido pelo dispositivo e pode ser qualquer status.

### <a name="amqp"></a>AMQP

A seção a seguir é para o protocolo AMQP.

#### <a name="method-invocation"></a>Invocação de método

O dispositivo recebe pedidos de método direto através da criação de um link de receção no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A mensagem AMQP chega no link de recebimento que representa a solicitação do método. Contém as seguintes secções:

* A propriedade de ID de correlação, que contém uma ID de solicitação que deve ser passada de volta com a resposta do método correspondente.

* Uma propriedade de aplicação chamada `IoThub-methodname`, que contém o nome do método que está a ser invocado.

* O corpo da mensagem AMQP que contém a carga do método como JSON.

#### <a name="response"></a>Resposta

O dispositivo cria um link de envio para devolver a resposta do método no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A resposta do método é retornada no link de envio e é estruturada da seguinte maneira:

* A propriedade de ID de correlação, que contém a ID da solicitação passada na mensagem de solicitação do método.

* Uma propriedade de aplicação chamada `IoThub-status`, que contém o estado do método fornecido pelo utilizador.

* O corpo da mensagem AMQP que contém a resposta do método como JSON.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia do desenvolvedor do Hub IoT incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas que se aplicam e o comportamento de estrangulamento que se espera quando se usa o IoT Hub.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta ioT Hub para gémeos de dispositivos, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta IoT Hub que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar métodos diretos, você pode estar interessado no seguinte artigo do guia do desenvolvedor do Hub IoT:

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez esteja interessado no seguinte tutorial do Hub IoT:

* [Utilizar métodos diretos](quickstart-control-device-node.md)
* [Gestão de dispositivos com ferramentas Azure IoT para código VS](iot-hub-device-management-iot-toolkit.md)
