---
title: Desenvolver módulos do Azure IoT Edge | Documentos da Microsoft
description: Desenvolver módulos personalizados para o Azure IoT Edge que possa comunicar com o tempo de execução e o IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ea2d1dbcf991ce858f07905bc1bb41809f9bcdd8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882744"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolver seus próprios módulos do IoT Edge

Módulos do IoT Edge do Azure podem ligar-se com outros serviços do Azure e contribuir para o pipeline de dados de cloud maior. Este artigo descreve como pode desenvolver módulos para comunicar com o runtime do IoT Edge e IoT Hub e, portanto, o restante da cloud do Azure. 

## <a name="iot-edge-runtime-environment"></a>Ambiente de runtime do IoT Edge
O runtime do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implementá-las em dispositivos IoT Edge. Num alto nível, qualquer programa pode ser empacotado como um módulo do IoT Edge. No entanto, para tirar partido do IoT Edge, comunicação e funcionalidades de gestão, um programa em execução num módulo pode ligar-se para o hub IoT Edge local, integrado no runtime do IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Utilizar o hub IoT Edge
O hub IoT Edge fornece duas funcionalidades principais: proxy para o IoT Hub e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos de IoT Hub
IoT Hub vê uma instância de módulo analogously para um dispositivo, no sentido de que:

* tem um duplo do módulo que é distinto e isolado a partir do [dispositivo duplo](../iot-hub/iot-hub-devguide-device-twins.md) e os outros duplos de módulo de que o dispositivo;
* Pode enviar [mensagens dispositivo-para-cloud](../iot-hub/iot-hub-devguide-messaging.md);
* pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) direcionado especificamente para a sua identidade.

Atualmente, um módulo não é possível receber mensagens da cloud para dispositivo nem utilizar a funcionalidade de carregamento de ficheiros.

Ao escrever um módulo, pode utilizar o [SDK de dispositivo do IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para se ligar ao hub IoT Edge e utilizar a funcionalidade acima, tal como faria ao utilizar o IoT Hub com um aplicativo de dispositivo, a única diferença é que, a partir da sua aplicação back-end, terá de fazer referência à identidade do módulo em vez da identidade de dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud
Para ativar a complexa de processamento de mensagens do dispositivo para a cloud, hub do IoT Edge fornece declarativa de encaminhamento de mensagens entre módulos e entre módulos e o IoT Hub. Encaminhamento declarativa permite que os módulos interceptar e processar mensagens enviadas por outros módulos e propagação-las em pipelines de complexos. Para obter mais informações, consulte [implementar módulos e estabelecer as rotas no IoT Edge](module-composition.md).

Módulo do IoT Edge, em vez de um aplicativo de dispositivo do IoT Hub normal, pode receber mensagens de dispositivo para a nuvem que estão a ser transmitidas por proxy ao seu hub IoT Edge local para processá-las.

Hub do IoT Edge propaga as mensagens ao módulo com base nas rotas declarativas descritas na [manifesto de implantação](module-composition.md). Ao desenvolver um módulo do IoT Edge, pode receber essas mensagens através da definição de manipuladores de mensagens.

Para simplificar a criação de rotas, IoT Edge adiciona o conceito de módulo *entrada* e *saída* pontos de extremidade. Um módulo pode receber todas as mensagens de dispositivo-para-cloud encaminhadas para o mesmo sem especificar qualquer entrada e pode enviar mensagens dispositivo-para a cloud sem especificar quaisquer dados. No entanto, uso explícitas entradas e saídas, torna as regras de roteamento mais simples de entender. 

Por fim, as mensagens de dispositivo-para-cloud manipuladas pelo Edge hub são marcadas com as seguintes propriedades do sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | O ID de dispositivo do cliente que enviou a mensagem |
| $connectionModuleId | O ID de módulo do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode estar vazio. |
| $outputName | A saída utilizada para enviar a mensagem. Pode estar vazio. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligar ao hub IoT Edge, a partir de um módulo
Ligar ao hub IoT Edge local a partir de um módulo envolve dois passos: 
1. Crie uma instância de ModuleClient na sua aplicação.
2. Certifique-se de que seu aplicativo aceita o certificado apresentado pelo hub IoT Edge nesse dispositivo.

Crie uma instância de ModuleClient para ligar o seu módulo para o hub do IoT Edge em execução no dispositivo, semelhante a como instâncias de DeviceClient ligar dispositivos IoT ao IoT Hub. Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência da API para seu idioma preferencial do SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C e Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), ou [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Passos Seguintes

[Preparar o ambiente de teste e desenvolvimento para o IoT Edge](development-environment.md)

[Utilizar o Visual Studio 2017 para desenvolver C# módulos do IoT Edge](how-to-visual-studio-develop-csharp-module.md)

[Utilizar o Visual Studio Code para desenvolver módulos do IoT Edge](how-to-vs-code-develop-module.md)

