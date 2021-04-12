---
title: Desenvolver módulos para Azure IoT Edge | Microsoft Docs
description: Desenvolver módulos personalizados para Azure IoT Edge que podem comunicar com o tempo de funcionamento e ioT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6274d0f0265220877938775ab6efd72ff22b84e4
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031134"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva os seus próprios módulos IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Os módulos Azure IoT Edge podem conectar-se com outros serviços Azure e contribuir para o seu maior pipeline de dados em nuvem. Este artigo descreve como pode desenvolver módulos para comunicar com o tempo de execução IoT Edge e ioT Hub, e, portanto, o resto da nuvem Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução IoT Edge

O tempo de funcionamento do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos IoT Edge e implantá-los em dispositivos IoT Edge. Qualquer programa pode ser embalado como um módulo IoT Edge. Para tirar o máximo partido das funcionalidades de comunicação e gestão IoT Edge, um programa em execução num módulo pode utilizar o Azure IoT Device SDK para ligar ao hub local IoT Edge.
::: moniker range=">=iotedge-2020-11"
Os módulos também podem usar qualquer cliente MQTT para se conectar ao corretor MQTT do hub IoT Edge local.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Embalar o seu programa como um módulo IoT Edge

Para implantar o seu programa num dispositivo IoT Edge, este deve primeiro ser contentorizado e executado com um motor compatível com Docker. IoT Edge usa [Moby,](https://github.com/moby/moby)o projeto de código aberto por trás de Docker, como o seu motor compatível com Docker. Os mesmos parâmetros a que estás habituado com o Docker podem ser passados para os teus módulos IoT Edge. Para obter mais informações, consulte [Como configurar o recipiente para criar opções para módulos IoT Edge](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Usando o hub IoT Edge

O hub IoT Edge fornece duas funcionalidades principais: proxy para IoT Hub, e comunicações locais.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligação ao hub IoT Edge a partir de um módulo

A ligação ao hub local IoT Edge a partir de um módulo envolve os mesmos passos de ligação que para qualquer cliente. Para obter mais informações, consulte ['Ligar-se ao hub IoT Edge'.](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)

Para utilizar o encaminhamento IoT Edge sobre AMQP ou MQTT, pode utilizar o MóduloClient a partir do Azure IoT SDK. Crie uma instância ModuleClient para ligar o seu módulo ao hub IoT Edge em funcionamento no dispositivo, semelhante à forma como as instâncias DeviceClient ligam dispositivos IoT ao IoT Hub. Para obter mais informações sobre a classe MóduloClient e seus métodos de comunicação, consulte a referência API para a sua língua SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C,](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient) [Java,](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para utilizar o corretor MQTT IoT Edge, precisa de trazer o seu próprio cliente MQTT e iniciar a ligação consigo mesmo com informações que obtém da API de carga de trabalho do daemon IoT Edge. <!--Need to add details here-->

Para obter mais informações sobre a escolha entre o encaminhamento ou a publicação/subscrição com o corretor MQTT, consulte a [comunicação local](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitivos de corretor MQTT

#### <a name="send-a-message-on-a-user-defined-topic"></a>Envie uma mensagem sobre um tópico definido pelo utilizador

Com o corretor IoT Edge MQTT, pode publicar mensagens em quaisquer tópicos definidos pelo utilizador. Para tal, autorize o seu módulo a publicar sobre tópicos específicos e, em seguida, obtenha um sinal da carga de trabalho API para usar como senha ao ligar-se ao corretor MQTT e, finalmente, publique mensagens sobre os tópicos autorizados com o cliente MQTT à sua escolha.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Receba mensagens num tópico definido pelo utilizador

Com o corretor IoT Edge MQTT, receber mensagens é semelhante. Primeiro certifique-se de que o seu módulo está autorizado a subscrever tópicos específicos, em seguida, obter um token da carga de trabalho API para usar como senha ao ligar ao corretor MQTT, e finalmente subscrever mensagens sobre os tópicos autorizados com o cliente MQTT à sua escolha.

::: moniker-end

### <a name="iot-hub-primitives"></a>Primitivos IoT Hub

O IoT Hub vê uma instância de módulo análoga a um dispositivo, no sentido de que:

* pode enviar [mensagens dispositivo-a-nuvem;](../iot-hub/iot-hub-devguide-messaging.md)
* pode receber [métodos diretos direcionados](../iot-hub/iot-hub-devguide-direct-methods.md) especificamente para a sua identidade.
* tem um módulo gémeo que é distinto e isolado do [dispositivo gémeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros gémeos módulos desse dispositivo;

Atualmente, os módulos não podem receber mensagens nuvem-para-dispositivo ou utilizar a funcionalidade de upload de ficheiros.

Ao escrever um módulo, pode ligar-se ao hub IoT Edge e utilizar primitivos IoT Hub como faria ao utilizar o IoT Hub com uma aplicação de dispositivo. A única diferença entre os módulos IoT Edge e as aplicações do dispositivo IoT é que tem de se referir à identidade do módulo em vez da identidade do dispositivo.

#### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud

Um módulo IoT Edge pode enviar mensagens para a nuvem através do hub IoT Edge que funciona como um corretor local e propaga mensagens para a nuvem. Para permitir o processamento complexo de mensagens dispositivo-nuvem, um módulo IoT Edge também pode intercetar e processar mensagens enviadas por outros módulos ou dispositivos para o seu hub IoT Edge local e enviar novas mensagens com dados processados. Cadeias de módulos IoT Edge podem assim ser criadas para construir gasodutos de processamento locais.

Para enviar mensagens de telemetria dispositivo-nuvem utilizando o encaminhamento, utilize o MóduloClient do Azure IoT SDK. Com o Azure IoT SDK, cada módulo tem o conceito de *pontos* finais de entrada e *saída* do módulo, que mapeiam para tópicos especiais de MQTT. Utilize o `ModuleClient.sendMessageAsync` método e enviará mensagens no ponto final de saída do seu módulo. Em seguida, configurar uma rota no EdgeHub para enviar este ponto final de saída para ioT Hub.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

O envio de mensagens de telemetria dispositivo-nuvem com o corretor MQTT é semelhante à publicação de mensagens em tópicos definidos pelo utilizador, mas utilizando o seguinte tópico especial IoT Hub para o seu módulo: `devices/<device_name>/<module_name>/messages/events` . As autorizações devem ser devidamente configurados. A ponte MQTT também deve ser configurada para encaminhar as mensagens sobre este tópico para a nuvem.

::: moniker-end

Para processar mensagens utilizando o encaminhamento, primeiro crie uma rota para enviar mensagens provenientes de outro ponto final (módulo ou dispositivo) para o ponto final de entrada do seu módulo e, em seguida, ouça mensagens no ponto final de entrada do seu módulo. Cada vez que uma nova mensagem volta, uma função de retorno é desencadeada pelo Azure IoT SDK. Processe a sua mensagem com esta função de retorno e envie opcionalmente novas mensagens na fila do ponto final do módulo.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

O processamento de mensagens que utilizam o corretor MQTT é semelhante ao que se subscreve a subscrição de mensagens em tópicos definidos pelo utilizador, mas utilizando os tópicos especiais IoT Edge da fila de saída do seu módulo: `devices/<device_name>/<module_name>/messages/events` . As autorizações devem ser devidamente configurados. Opcionalmente, pode enviar novas mensagens sobre os tópicos à sua escolha.

::: moniker-end

#### <a name="twins"></a>Gémeos

Gémeos são um dos primitivos fornecidos pelo IoT Hub. Existem documentos JSON que armazenam informações do Estado, incluindo metadados, configurações e condições. Cada módulo ou dispositivo tem o seu próprio gémeo.

Para obter um módulo twin com o Azure IoT SDK, ligue para o `ModuleClient.getTwin` método.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para obter um módulo twin com qualquer cliente MQTT, um pouco mais de trabalho está envolvido, uma vez que conseguir um gémeo não é um padrão típico de MQTT. O módulo deve primeiro subscrever o tópico especial IoT Hub `$iothub/twin/res/#` . Este nome tópico é herdado do IoT Hub, e todos os dispositivos/módulos precisam subscrever o mesmo tópico. Não significa que os dispositivos recebam o gémeo uns dos outros. O IoT Hub e o EdgeHub sabem qual o gémeo que deve ser entregue onde, mesmo que todos os dispositivos ouçam o mesmo nome de tópico. Uma vez feita a subscrição, o módulo precisa de pedir o gémeo publicando uma mensagem para o seguinte tópico especial IoT Hub com um ID de pedido `$iothub/twin/GET/?$rid=1234` . Este ID de pedido é um ID arbitrário (isto é, um GUID), que será devolvido pelo IoT Hub juntamente com os dados solicitados. É assim que um cliente pode emparelhar os seus pedidos com as respostas. O código de resultados é um código de estado http-like, onde o sucesso é codificado como 200.

::: moniker-end

Para receber um patch twin módulo com o Azure IoT SDK, implemente uma função de retorno e registe-a com o `ModuleClient.moduleTwinCallback` método do Azure IoT SDK para que a sua função de retorno seja ativada cada vez que entra um patch duplo.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para receber um patch twin módulo com qualquer cliente MQTT, o processo é muito semelhante a receber gémeos completos: um cliente precisa subscrever o tópico especial IoT Hub `$iothub/twin/PATCH/properties/desired/#` . Após a subscrição ser feita, quando o IoT Hub envia uma alteração da secção desejada do gémeo, o cliente recebe-a.

::: moniker-end

#### <a name="receive-direct-methods"></a>Receber métodos diretos

Para receber um método direto com o Azure IoT SDK, implemente uma função de retorno e registe-a com o `ModuleClient.methodCallback` método a partir do Azure IoT SDK para que a sua função de retorno seja ativada cada vez que um método direto entra.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Para receber um método direto com qualquer cliente MQTT, o processo é muito semelhante a receber patches duplos. O cliente precisa confirmar que recebeu a chamada e pode enviar algumas informações ao mesmo tempo. O tópico especial IoT Hub para subscrever é `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Apoio à linguagem e arquitetura

O IoT Edge suporta vários sistemas operativos, arquiteturas de dispositivos e linguagens de desenvolvimento para que possa construir o cenário que corresponda às suas necessidades. Utilize esta secção para compreender as suas opções para desenvolver módulos IoT Edge personalizados. Pode aprender mais sobre suporte de ferramentas e requisitos para cada idioma em [Preparar o seu ambiente de desenvolvimento e teste para ioT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todas as línguas da tabela seguinte, o IoT Edge suporta o desenvolvimento de recipientes AMD64 e ARM32 Linux.

| Linguagem de programação | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| C# | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O apoio ao desenvolvimento e depuragem dos contentores ARM64 Linux está em [visualização pública.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Para obter mais informações, consulte [os módulos Develop and debug ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Para todos os idiomas na tabela seguinte, o IoT Edge suporta o desenvolvimento de recipientes AMD64 Windows.

| Linguagem de programação | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Estúdio Visual 2017/2019 |
| C# | Código do Estúdio Visual (sem capacidades de depuração)<br>Estúdio Visual 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1.1 LTS é o último canal de lançamento que suporta recipientes Windows. A partir da versão 1.2, os recipientes windows não são suportados.

Para obter informações sobre o desenvolvimento com recipientes Windows, consulte a versão [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) deste artigo.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Passos seguintes

[Prepare o seu ambiente de desenvolvimento e teste para ioT Edge](development-environment.md)

[Use o Estúdio Visual para desenvolver módulos C# para IoT Edge](how-to-visual-studio-develop-module.md)

[Use o Código do Estúdio Visual para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Compreender e utilizar os SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md)
