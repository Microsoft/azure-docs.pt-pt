---
title: Compreender os métodos diretos do Hub Azure IoT [ Microsoft Docs
description: Guia de desenvolvedores - utilize métodos diretos para invocar código nos seus dispositivos a partir de uma aplicação de serviço.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: d4040a4d0cf3fadf7a6e07c0e03e105975d17040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499260"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Compreender e invocar métodos diretos a partir do Hub IoT

O IoT Hub dá-lhe a capacidade de invocar métodos diretos em dispositivos a partir da nuvem. Os métodos diretos representam uma interação de resposta a pedido com um dispositivo semelhante a uma chamada HTTP em que eles têm sucesso ou falham imediatamente (após um tempo limite especificado pelo utilizador). Esta abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método do dispositivo visa um único dispositivo. [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) mostra como fornecer uma forma de invocar métodos diretos em vários dispositivos, e agendar a invocação do método para dispositivos desligados.

Qualquer pessoa com permissões de **ligação** de serviço no IoT Hub pode invocar um método num dispositivo.

Os métodos diretos seguem um padrão de resposta a pedidos e destinam-se a comunicações que requerem confirmação imediata do seu resultado. Por exemplo, o controlo interativo do dispositivo, como ligar uma ventoinha.

Consulte a orientação de [comunicação Cloud-to-device,](iot-hub-devguide-c2d-guidance.md) em caso de dúvida entre a utilização de propriedades desejadas, métodos diretos ou mensagens cloud-to-device.

## <a name="method-lifecycle"></a>Ciclo de vida do método

São implementados métodos diretos no dispositivo e podem exigir zero ou mais inputs na carga útil do método para serem corretamente instantâneos. Invoca um método direto através de`{iot hub}/twins/{device id}/methods/`um URI virado para o serviço ( ). Um dispositivo recebe métodos diretos através de um`$iothub/methods/POST/{method name}/`tópico MQTT específico `IoThub-methodname` `IoThub-status` do dispositivo ( ) ou através de links AMQP (as propriedades e aplicações). 

> [!NOTE]
> Quando invoca um método direto num dispositivo, os nomes e valores de propriedade só podem conter alfanumérico imprimível US-ASCII, exceto qualquer um no seguinte conjunto:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Os métodos diretos são sincronizados e ou têm sucesso ou falham após o período de tempo (padrão: 30 segundos, settable entre 5 e 300 segundos). Os métodos diretos são úteis em cenários interativos onde pretende que um dispositivo aja se e apenas se o dispositivo estiver online e receber comandos. Por exemplo, acender uma luz de um telefone. Nestes cenários, pretende-se ver um sucesso imediato ou um fracasso para que o serviço de nuvem possa agir o mais rapidamente possível. O dispositivo pode devolver algum corpo de mensagem como resultado do método, mas não é necessário para que o método o faça. Não há garantias sobre a encomenda ou qualquer semântica conmoedada em chamadas de métodos.

Os métodos diretos são https-apenas do lado da nuvem, e MQTT ou AMQP do lado do dispositivo.

A carga útil para pedidos e respostas de métodos é um documento JSON até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invoque um método direto a partir de uma aplicação back-end

Agora, invoque um método direto de uma aplicação back-end.

### <a name="method-invocation"></a>Invocação do método

As invocações diretas de métodonum dispositivo são chamadas HTTPS que são compostas pelos seguintes itens:

* O *pedido URI* específico do dispositivo juntamente com a versão [API:](/rest/api/iothub/service/devicemethod/invokedevicemethod)

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

O valor `responseTimeoutInSeconds` fornecido como no pedido é o tempo que o serviço IoT Hub deve aguardar para a conclusão de uma execução de método direto num dispositivo. Estabeleça este prazo para ser pelo menos o tempo esperado de execução de um método direto por um dispositivo. Se o tempo não for fornecido, o valor padrão de 30 segundos é utilizado. Os valores mínimos e máximos para `responseTimeoutInSeconds` são de 5 e 300 segundos, respectivamente.

O valor `connectTimeoutInSeconds` fornecido como no pedido é o tempo após a invocação de um método direto que o serviço IoT Hub deve aguardar que um dispositivo desligado entre online. O valor predefinido é 0, o que significa que os dispositivos já devem estar online após a invocação de um método direto. O valor `connectTimeoutInSeconds` máximo para 300 segundos.


#### <a name="example"></a>Exemplo

Veja abaixo um exemplo `curl`de sosses usando . 

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

A aplicação back-end recebe uma resposta que é composta dos seguintes itens:

* Código de *estado HTTP:*
  * 200 indica execução bem sucedida do método direto;
  * 404 indica que ou o ID do dispositivo é inválido ou que o `connectTimeoutInSeconds` dispositivo não estava on-line após a invocação de um método direto e para posteriormente (utilize uma mensagem de erro acompanhada para compreender a causa raiz);
  * 504 indica tempo de saída causado pelo dispositivo que `responseTimeoutInSeconds`não responde a uma chamada de método direto dentro de .

* *Cabeçalhos* que contenham o ETag, solicitar ID, tipo de conteúdo e codificação de conteúdo.

* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Ambos `status` `body` são fornecidos pelo dispositivo e utilizados para responder com o código de estado e/ou descrição do próprio dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos IoT Edge

Invocar métodos diretos utilizando um id de módulo é suportado no [IoT Service Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Para o efeito, `ServiceClient.InvokeDeviceMethodAsync()` utilize o `deviceId` método `moduleId` e passe nos e como parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Manuseie um método direto num dispositivo

Vamos ver como lidar com um método direto num dispositivo IoT.

### <a name="mqtt"></a>MQTT

A seguinte secção é para o protocolo MQTT.

#### <a name="method-invocation"></a>Invocação do método

Os dispositivos recebem pedidos de método direto `$iothub/methods/POST/{method name}/?$rid={request id}`sobre o tópico MQTT: . O número de subscrições por dispositivo está limitado a 5. Por conseguinte, recomenda-se que não subscreva individualmente cada método direto. Em vez disso, considere subscrever `$iothub/methods/POST/#` e, em seguida, filtrar as mensagens entregues com base nos nomes dos métodos pretendidos.

O corpo que o dispositivo recebe está no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Os pedidos de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia `$iothub/methods/res/{status}/?$rid={request id}`respostas para, onde:

* A `status` propriedade é o estado fornecido pelo dispositivo de execução do método.

* A `$rid` propriedade é o pedido de identificação do método invocação recebida do IoT Hub.

O corpo é definido pelo dispositivo e pode ser qualquer estado.

### <a name="amqp"></a>AMQP

A secção seguinte é para o protocolo AMQP.

#### <a name="method-invocation"></a>Invocação do método

O dispositivo recebe pedidos de método direto através `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`da criação de um link de receção no endereço .

A mensagem AMQP chega no link de receção que representa o pedido do método. Contém as seguintes secções:

* A propriedade de ID de correlação, que contém um ID de pedido que deve ser retransmitida com a resposta do método correspondente.

* Uma propriedade `IoThub-methodname`de aplicação denominada , que contém o nome do método que está a ser invocado.

* O corpo de mensagem AMQP contendo a carga útil do método como JSON.

#### <a name="response"></a>Resposta

O dispositivo cria um link de envio `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`para devolver a resposta do método no endereço .

A resposta do método é devolvida no link de envio e é estruturada da seguinte forma:

* A propriedade de ID de correlação, que contém o ID de pedido passado na mensagem de pedido do método.

* Uma propriedade `IoThub-status`de aplicação denominada , que contém o estado do método fornecido pelo utilizador.

* O corpo de mensagem AMQP contendo a resposta do método como JSON.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas que se aplicam e o comportamento de estrangulamento que se espera quando se usa o IoT Hub.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta ioT Hub para gémeos de dispositivos, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta IoT Hub que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar métodos diretos, pode estar interessado no seguinte artigo de guia de desenvolvimento do IoT Hub:

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se quiser experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado no seguinte tutorial ioT Hub:

* [Utilizar métodos diretos](quickstart-control-device-node.md)
* [Gestão de dispositivos com as Ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)
