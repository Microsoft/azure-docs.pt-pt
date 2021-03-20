---
title: Compreenda os métodos diretos do Azure IoT Hub | Microsoft Docs
description: Developer guide - use métodos diretos para invocar código nos seus dispositivos a partir de uma aplicação de serviço.
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: b75e859fc1237bc88bee464cef423b7289810fa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147796"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Compreender e invocar métodos diretos a partir do Hub IoT

O IoT Hub dá-lhe a capacidade de invocar métodos diretos em dispositivos a partir da nuvem. Os métodos diretos representam uma interação de resposta de pedido com um dispositivo semelhante a uma chamada HTTP na medida em que eles sucedem ou falham imediatamente (após um tempo limite especificado pelo utilizador). Esta abordagem é útil para cenários onde as medidas imediatas variam consoante a capacidade de resposta do dispositivo.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método do dispositivo tem como alvo um único dispositivo. [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) mostra como fornecer uma maneira de invocar métodos diretos em vários dispositivos, e agendar a invocação do método para dispositivos desligados.

Qualquer pessoa com permissões **de ligação** de serviço no IoT Hub pode invocar um método num dispositivo.

Os métodos diretos seguem um padrão de resposta de pedido e destinam-se a comunicações que requerem confirmação imediata do seu resultado. Por exemplo, o controlo interativo do dispositivo, como ligar uma ventoinha.

Consulte a [orientação de comunicação Nuvem-a-dispositivo](iot-hub-devguide-c2d-guidance.md) em caso de dúvida entre a utilização de propriedades desejadas, métodos diretos ou mensagens nuvem-dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Os métodos diretos são implementados no dispositivo e podem exigir zero ou mais entradas na carga útil do método para instantanear corretamente. Invoca um método direto através de um URI virado para o serviço ( `{iot hub}/twins/{device id}/methods/` ). Um dispositivo recebe métodos diretos através de um tópico MQTT específico do dispositivo ( `$iothub/methods/POST/{method name}/` ) ou através de ligações AMQP (as propriedades e `IoThub-methodname` `IoThub-status` aplicações).

> [!NOTE]
> Quando invoca um método direto num dispositivo, os nomes e valores de propriedade só podem conter alfanumérico imprimível US-ASCII, exceto qualquer um no seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Os métodos diretos são sincronizados e sucedem ou falham após o período de tempo limite (padrão: 30 segundos, padrão entre 5 e 300 segundos). Os métodos diretos são úteis em cenários interativos onde pretende que um dispositivo aja se e somente se o dispositivo estiver online e receber comandos. Por exemplo, acender uma luz de um telefone. Nestes cenários, pretende-se um sucesso ou falha imediato para que o serviço de cloud possa atuar o mais rapidamente possível. O dispositivo pode devolver algum corpo de mensagem como resultado do método, mas não é necessário para o método fazê-lo. Não há garantias sobre a ordem ou qualquer semântica de conucção em chamadas metódãs.

Os métodos diretos são apenas HTTPS do lado da nuvem e MQTT, AMQP, MQTT sobre WebSockets, ou AMQP sobre WebSockets do lado do dispositivo.

A carga útil para pedidos e respostas de métodos é um documento JSON até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto a partir de uma aplicação back-end

Agora, invoque um método direto a partir de uma aplicação de back-end.

### <a name="method-invocation"></a>Invocação do método

Invocações de métodos diretos num dispositivo são chamadas HTTPS que são compostas pelos seguintes itens:

* O *pedido URI* específico para o dispositivo juntamente com a [versão API:](/rest/api/iothub/service/devices/invokemethod)

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* O *método* POST

* *Cabeçalhos* que contêm a autorização, pedido de identificação, tipo de conteúdo e codificação de conteúdo.

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

O valor fornecido como `responseTimeoutInSeconds` no pedido é o tempo que o serviço IoT Hub deve aguardar para a conclusão de uma execução de método direto num dispositivo. Desacampeee este prazo para ser pelo menos o tempo de execução esperado de um método direto por um dispositivo. Se não for fornecido o tempo limite, utiliza-se o valor predefinido de 30 segundos. Os valores mínimos e máximos `responseTimeoutInSeconds` para 5 e 300 segundos, respectivamente.

O valor fornecido como `connectTimeoutInSeconds` no pedido é o tempo após a invocação de um método direto que o serviço IoT Hub deve aguardar para que um dispositivo desligado fique online. O valor padrão é 0, o que significa que os dispositivos já devem estar on-line após a invocação de um método direto. O valor máximo é de `connectTimeoutInSeconds` 300 segundos.

#### <a name="example"></a>Exemplo

Este exemplo permitir-lhe-á iniciar de forma segura um pedido de invocação de um Método Direto num dispositivo IoT registado num Hub IoT Azure.

Para começar, utilize a [extensão IoT do Microsoft Azure para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para criar uma SharedAccessSignature.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Em seguida, substitua o cabeçalho de Autorização pelo seu recém-gerado SharedAccesssignature, em seguida, modifique o `iothubName` `deviceId` , e `methodName` `payload` parâmetros para combinar a sua implementação no comando de exemplo `curl` abaixo.  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
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

Execute o comando modificado para invocar o Método Direto especificado. Os pedidos bem sucedidos devolverão um código de estado HTTP 200.

> [!NOTE]
> O exemplo acima demonstra a invocação de um Método Direto num dispositivo.  Se desejar invocar um Método Direto num Módulo IoT Edge, terá de modificar o pedido de url como mostrado abaixo:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Resposta

A aplicação back-end recebe uma resposta que é composta pelos seguintes itens:

* *Código de estado HTTP*:
  * 200 indica uma execução bem sucedida do método direto;
  * 404 indica que qualquer um dos dispositivos de ID é inválido, ou que o dispositivo não estava on-line após a invocação de um método direto e para `connectTimeoutInSeconds` a partir daí (utilizar mensagem de erro acompanhada para compreender a causa raiz);
  * 504 indica tempo de passagem causado pelo dispositivo que não responde a uma chamada de método direto no interior `responseTimeoutInSeconds` .

* *Cabeçalhos* que contêm o ETag, solicitar ID, tipo de conteúdo e codificação de conteúdo.

* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Ambos `status` são fornecidos pelo dispositivo e `body` utilizados para responder com o código de estado e/ou descrição do próprio dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos IoT Edge

Invocar métodos diretos utilizando um módulo ID é suportado no [Serviço IoT Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Para o efeito, utilize o `ServiceClient.InvokeDeviceMethodAsync()` método e passe no e como `deviceId` `moduleId` parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Manuseie um método direto num dispositivo

Vamos ver como lidar com um método direto num dispositivo IoT.

### <a name="mqtt"></a>MQTT

A seguinte secção é para o protocolo MQTT.

#### <a name="method-invocation"></a>Invocação do método

Os dispositivos recebem pedidos de método direto sobre o tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}` . O número de subscrições por dispositivo é limitado a 5. Por conseguinte, recomenda-se não subscrever individualmente cada método direto. Em vez disso, considere subscrever `$iothub/methods/POST/#` e, em seguida, filtrar as mensagens entregues com base nos nomes do método pretendido.

O corpo que o dispositivo recebe encontra-se no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Os pedidos de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia respostas `$iothub/methods/res/{status}/?$rid={request id}` para, quando:

* A `status` propriedade é o estado fornecido pelo dispositivo de execução do método.

* A `$rid` propriedade é o ID pedido da invocação do método recebido do IoT Hub.

O corpo é definido pelo dispositivo e pode ser qualquer estado.

### <a name="amqp"></a>AMQP

A seguinte secção é para o protocolo AMQP.

#### <a name="method-invocation"></a>Invocação do método

O dispositivo recebe pedidos de método direto criando um link de receção no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

A mensagem AMQP chega no link de receção que representa o pedido de método. Contém as seguintes secções:

* A propriedade de ID de correlação, que contém um ID de pedido que deve ser repercutido com a resposta do método correspondente.

* Uma propriedade de aplicação chamada `IoThub-methodname` , que contém o nome do método que está sendo invocado.

* O corpo de mensagem AMQP que contém a carga útil do método como JSON.

#### <a name="response"></a>Resposta

O dispositivo cria um link de envio para devolver a resposta do método no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

A resposta do método é devolvida no link de envio e estruturada da seguinte forma:

* A propriedade de identificação de correlação, que contém o ID do pedido passado na mensagem de pedido do método.

* Uma propriedade de aplicação chamada `IoThub-status` , que contém o estado do método fornecido pelo utilizador.

* O corpo de mensagens AMQP que contém a resposta do método como JSON.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de tempo de execução e gestão.

* [O estrangulamento e as quotas](iot-hub-devguide-quotas-throttling.md) descrevem as quotas que se aplicam e o comportamento de estrangulamento que se espera quando se usa o Hub IoT.

* [O dispositivo e serviço Azure IoT lista](iot-hub-devguide-sdks.md) os vários SDKs de linguagem que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* [IoT Hub linguagem de consulta para gémeos de dispositivos, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta IoT Hub que você pode usar para obter informações do IoT Hub sobre os gémeos e empregos do seu dispositivo.

* [O suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar métodos diretos, poderá estar interessado no seguinte artigo de guia de desenvolvimento do IoT Hub:

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se quiser experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado no seguinte tutorial do IoT Hub:

* [Utilizar métodos diretos](quickstart-control-device-node.md)
* [Gestão de dispositivos com as Ferramentas do Azure IoT para VS Code](iot-hub-device-management-iot-toolkit.md)