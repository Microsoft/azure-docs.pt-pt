---
title: Entender os métodos diretos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – Use métodos diretos para invocar o código em seus dispositivos de um aplicativo de serviço.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: f4125aae954519beead99db45fc8a35264d5731e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429276"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Entender e invocar métodos diretos do Hub IoT

O Hub IoT oferece a capacidade de invocar métodos diretos em dispositivos da nuvem. Os métodos diretos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada HTTP em que eles são bem-sucedidos ou falham imediatamente (após um tempo limite especificado pelo usuário). Essa abordagem é útil para cenários em que o curso de ação imediata é diferente dependendo se o dispositivo foi capaz de responder.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Cada método de dispositivo tem como alvo um único dispositivo. [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md) mostra como fornecer uma maneira de invocar métodos diretos em vários dispositivos e agendar a invocação de método para dispositivos desconectados.

Qualquer pessoa com permissões de **conexão de serviço** no Hub IOT pode invocar um método em um dispositivo.

Os métodos diretos seguem um padrão de solicitação-resposta e destinam-se a comunicações que exigem confirmação imediata de seus resultados. Por exemplo, controle interativo do dispositivo, como ativar um ventilador.

Consulte [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) se estiver em dúvida entre usar as propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Os métodos diretos são implementados no dispositivo e podem exigir zero ou mais entradas na carga do método para instanciar corretamente. Você invoca um método direto por meio de um URI voltado para o serviço (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos por meio de um tópico MQTT específico do dispositivo (`$iothub/methods/POST/{method name}/`) ou por meio de links do AMQP (as propriedades `IoThub-methodname` e `IoThub-status` aplicativo). 

> [!NOTE]
> Quando você invoca um método direto em um dispositivo, os nomes e valores de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto no seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Os métodos diretos são síncronos e são bem-sucedidos ou falham após o período de tempo limite (padrão: 30 segundos, configurável até 300 segundos). Os métodos diretos são úteis em cenários interativos em que você deseja que um dispositivo aja se e somente se o dispositivo estiver online e recebendo comandos. Por exemplo, ativar uma luz de um telefone. Nesses cenários, você deseja ver um êxito ou uma falha imediata para que o serviço de nuvem possa agir no resultado assim que possível. O dispositivo pode retornar um corpo de mensagem como resultado do método, mas não é necessário para o método fazer isso. Não há nenhuma garantia de ordenação ou de qualquer semântica de simultaneidade em chamadas de método.

Os métodos diretos são somente HTTPS do lado da nuvem e MQTT ou AMQP do lado do dispositivo.

A carga para solicitações de método e respostas é um documento JSON de até 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto de um aplicativo de back-end

Agora, invoque um método direto de um aplicativo de back-end.

### <a name="method-invocation"></a>Invocação de método

Invocações de método direto em um dispositivo são chamadas HTTPS que são feitas dos seguintes itens:

* O *URI de solicitação* específico para o dispositivo junto com a [versão da API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* O *método* post

* *Cabeçalhos* que contêm a autorização, a ID da solicitação, o tipo de conteúdo e a codificação de conteúdo.

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

O tempo limite é em segundos. Se timeout não for definido, o padrão será 30 segundos.

#### <a name="example"></a>Exemplo

Veja abaixo um exemplo de forneceram usando `curl`. 

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

* *Código de status http*, que é usado para erros provenientes do Hub IOT, incluindo um erro 404 para dispositivos que não estão conectados no momento.

* *Cabeçalhos* que contêm a ETag, a ID da solicitação, o tipo de conteúdo e a codificação de conteúdo.

* Um *corpo* JSON no seguinte formato:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Tanto `status` quanto `body` são fornecidos pelo dispositivo e usados para responder com o código de status e/ou a descrição do dispositivo.

### <a name="method-invocation-for-iot-edge-modules"></a>Invocação de método para módulos de IoT Edge

A invocação de métodos diretos usando uma ID de módulo tem suporte no [SDK do cliente C# do serviço de IOT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Para essa finalidade, use o método `ServiceClient.InvokeDeviceMethodAsync()` e passe o `deviceId` e `moduleId` como parâmetros.

## <a name="handle-a-direct-method-on-a-device"></a>Manipular um método direto em um dispositivo

Vejamos como tratar um método direto em um dispositivo IoT.

### <a name="mqtt"></a>MQTT

A seção a seguir é para o protocolo MQTT.

#### <a name="method-invocation"></a>Invocação de método

Os dispositivos recebem solicitações de método direto no tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. O número de assinaturas por dispositivo é limitado a 5. Portanto, é recomendável não assinar cada método direto individualmente. Em vez disso, considere assinar `$iothub/methods/POST/#` e, em seguida, filtrar as mensagens entregues com base nos nomes de método desejados.

O corpo que o dispositivo recebe está no seguinte formato:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

As solicitações de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia respostas para `$iothub/methods/res/{status}/?$rid={request id}`, em que:

* A propriedade `status` é o status fornecido pelo dispositivo da execução do método.

* A propriedade `$rid` é a ID da solicitação da invocação do método recebida do Hub IoT.

O corpo é definido pelo dispositivo e pode ser qualquer status.

### <a name="amqp"></a>AMQP

A seção a seguir é para o protocolo AMQP.

#### <a name="method-invocation"></a>Invocação de método

O dispositivo recebe solicitações de método direto criando um link de recebimento no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A mensagem AMQP chega no link de recebimento que representa a solicitação do método. Ele contém as seguintes seções:

* A propriedade de ID de correlação, que contém uma ID de solicitação que deve ser passada de volta com a resposta do método correspondente.

* Uma propriedade de aplicativo chamada `IoThub-methodname`, que contém o nome do método que está sendo invocado.

* O corpo da mensagem AMQP que contém a carga do método como JSON.

#### <a name="response"></a>Resposta

O dispositivo cria um link de envio para retornar a resposta do método no endereço `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

A resposta do método é retornada no link de envio e é estruturada da seguinte maneira:

* A propriedade de ID de correlação, que contém a ID da solicitação passada na mensagem de solicitação do método.

* Uma propriedade de aplicativo chamada `IoThub-status`, que contém o status do método fornecido pelo usuário.

* O corpo da mensagem AMQP que contém a resposta do método como JSON.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia do desenvolvedor do Hub IoT incluem:

* Os [pontos de extremidade do Hub IOT](iot-hub-devguide-endpoints.md) descrevem os vários pontos de extremidade que cada Hub IOT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas que se aplicam e o comportamento de limitação esperado ao usar o Hub IOT.

* [SDKs de dispositivo e serviço do Azure IOT](iot-hub-devguide-sdks.md) lista os diversos SDKs de linguagem que você pode usar ao desenvolver aplicativos de dispositivo e de serviço que interagem com o Hub IOT.

* [Linguagem de consulta do Hub IOT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md) descreve a linguagem de consulta do Hub IOT que você pode usar para recuperar informações do Hub IOT sobre seu dispositivo gêmeos e trabalhos.

* [Suporte a MQTT do Hub IOT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IOT para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a usar métodos diretos, você pode estar interessado no seguinte artigo do guia do desenvolvedor do Hub IoT:

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez esteja interessado no seguinte tutorial do Hub IoT:

* [Usar métodos diretos](quickstart-control-device-node.md)
* [Gerenciamento de dispositivos com as ferramentas de IoT do Azure para VS Code](iot-hub-device-management-iot-toolkit.md)
