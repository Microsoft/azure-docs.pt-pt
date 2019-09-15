---
title: Desenvolver módulos do Azure IoT Edge | Documentos da Microsoft
description: Desenvolver módulos personalizados para o Azure IoT Edge que possa comunicar com o tempo de execução e o IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d983dc4a2623e7f2a272ea2a320d2658d784dee
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003581"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolver seus próprios módulos de IoT Edge

Azure IoT Edge módulos podem se conectar a outros serviços do Azure e contribuir para o pipeline de dados de nuvem maior. Este artigo descreve como você pode desenvolver módulos para se comunicar com o IoT Edge Runtime e o Hub IoT e, portanto, o restante da nuvem do Azure. 

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
Para habilitar o processamento complexo de mensagens do dispositivo para a nuvem, IoT Edge Hub fornece roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT. Encaminhamento declarativa permite que os módulos interceptar e processar mensagens enviadas por outros módulos e propagação-las em pipelines de complexos. Para obter mais informações, consulte [implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Módulo do IoT Edge, em vez de um aplicativo de dispositivo do IoT Hub normal, pode receber mensagens de dispositivo para a nuvem que estão a ser transmitidas por proxy ao seu hub IoT Edge local para processá-las.

O Hub de IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas no [manifesto de implantação](module-composition.md). Ao desenvolver um módulo do IoT Edge, pode receber essas mensagens através da definição de manipuladores de mensagens.

Para simplificar a criação de rotas, IoT Edge adiciona o conceito de pontos de extremidade de *entrada* e *saída* de módulo. Um módulo pode receber todas as mensagens de dispositivo-para-cloud encaminhadas para o mesmo sem especificar qualquer entrada e pode enviar mensagens dispositivo-para a cloud sem especificar quaisquer dados. No entanto, uso explícitas entradas e saídas, torna as regras de roteamento mais simples de entender. 

Por fim, as mensagens de dispositivo-para-cloud manipuladas pelo Edge hub são marcadas com as seguintes propriedades do sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | O ID de dispositivo do cliente que enviou a mensagem |
| $connectionModuleId | O ID de módulo do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode estar vazio. |
| $outputName | A saída utilizada para enviar a mensagem. Pode estar vazio. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligar ao hub IoT Edge, a partir de um módulo
Ligar ao hub IoT Edge local a partir de um módulo envolve dois passos: 
1. Crie uma instância do ModuleClient em seu aplicativo.
2. Certifique-se de que seu aplicativo aceita o certificado apresentado pelo hub IoT Edge nesse dispositivo.

Crie uma instância do ModuleClient para conectar seu módulo ao Hub de IoT Edge em execução no dispositivo, semelhante a como as instâncias do DeviceClient conectam dispositivos IoT ao Hub IoT. Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem de SDK preferida: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)ou [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Suporte à linguagem e à arquitetura

O IoT Edge dá suporte a vários sistemas operacionais, arquiteturas de dispositivo e linguagens de desenvolvimento para que você possa criar o cenário que atenda às suas necessidades. Use esta seção para entender as opções para o desenvolvimento de módulos de IoT Edge personalizados. Você pode saber mais sobre o suporte a ferramentas e requisitos para cada idioma em [preparar seu ambiente de desenvolvimento e teste para IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 e ARM32 Linux. 

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O desenvolvimento e a depuração de suporte para dispositivos ARM64 Linux estão em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [desenvolver e depurar módulos de IOT Edge do ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 Windows.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (sem recursos de depuração)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Passos Seguintes

[Prepare seu ambiente de desenvolvimento e teste para IoT Edge](development-environment.md)

[Use o Visual Studio para C# desenvolver módulos para IOT Edge](how-to-visual-studio-develop-module.md)

[Use Visual Studio Code para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Entender e usar SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md)