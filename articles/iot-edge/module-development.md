---
title: Desenvolver módulos para Azure IoT Edge Microsoft Docs
description: Desenvolver módulos personalizados para Azure IoT Edge que podem comunicar com o tempo de funcionamento e ioT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eae36f6b4baabdcc9831b084602d340a299a7bac
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047629"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva os seus próprios módulos IoT Edge

Os módulos Azure IoT Edge podem conectar-se com outros serviços Azure e contribuir para o seu maior pipeline de dados em nuvem. Este artigo descreve como pode desenvolver módulos para comunicar com o tempo de execução IoT Edge e ioT Hub, e, portanto, o resto da nuvem Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução IoT Edge

O tempo de funcionamento do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos IoT Edge e implantá-los em dispositivos IoT Edge. Qualquer programa pode ser embalado como um módulo IoT Edge. Para tirar o máximo partido das funcionalidades de comunicação e gestão IoT Edge, um programa em execução num módulo pode utilizar o Azure IoT Device SDK para ligar ao hub local IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Usando o hub IoT Edge

O hub IoT Edge fornece duas funcionalidades principais: proxy para IoT Hub, e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos IoT Hub

O IoT Hub vê uma instância de módulo análoga a um dispositivo, no sentido de que:

* tem um módulo gémeo que é distinto e isolado do [dispositivo gémeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros gémeos módulos desse dispositivo;
* pode enviar [mensagens dispositivo-a-nuvem;](../iot-hub/iot-hub-devguide-messaging.md)
* pode receber [métodos diretos direcionados](../iot-hub/iot-hub-devguide-direct-methods.md) especificamente para a sua identidade.

Atualmente, os módulos não podem receber mensagens nuvem-para-dispositivo ou utilizar a funcionalidade de upload de ficheiros.

Ao escrever um módulo, pode utilizar o [SDK do Dispositivo IoT Azure](../iot-hub/iot-hub-devguide-sdks.md) para ligar ao hub IoT Edge e utilizar a funcionalidade acima, como faria ao utilizar o IoT Hub com uma aplicação de dispositivo. A única diferença entre os módulos IoT Edge e as aplicações do dispositivo IoT é que tem de se referir à identidade do módulo em vez da identidade do dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud

Para permitir o processamento complexo de mensagens dispositivo-nuvem, o hub IoT Edge fornece o encaminhamento declarativo de mensagens entre módulos e entre módulos e IoT Hub. O encaminhamento declarativo permite que os módulos intercetem e processem mensagens enviadas por outros módulos e as propaguem em oleodutos complexos. Para obter mais informações, consulte [a implementação de módulos e estabeleça rotas em IoT Edge.](module-composition.md)

Um módulo IoT Edge, ao contrário de uma aplicação normal do dispositivo IoT Hub, pode receber mensagens de dispositivo para nuvem que estão a ser proximamente pelo seu hub IoT Edge local para processá-las.

O hub IoT Edge propaga as mensagens ao seu módulo com base em rotas declarativas descritas no [manifesto de implantação](module-composition.md). Ao desenvolver um módulo IoT Edge, pode receber estas mensagens definindo os manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge adiciona o conceito de *entradas* de módulos e pontos finais de *saída.* Um módulo pode receber todas as mensagens de dispositivo para nuvem encaminhadas para ele sem especificar qualquer entrada, e pode enviar mensagens de dispositivo para nuvem sem especificar qualquer saída. No entanto, a utilização de entradas e saídas explícitas torna as regras de encaminhamento mais simples de entender.

Finalmente, as mensagens dispositivo-a-nuvem tratadas pelo hub Edge são carimbadas com as seguintes propriedades do sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | A iD do dispositivo do cliente que enviou a mensagem |
| $connectionModuleId | O módulo ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode estar vazio. |
| $outputName | A saída usada para enviar a mensagem. Pode estar vazio. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligação ao hub IoT Edge a partir de um módulo

A ligação ao hub local IoT Edge a partir de um módulo envolve dois passos:

1. Crie um exemplo de MóduloClient na sua aplicação.
2. Certifique-se de que a sua candidatura aceita o certificado apresentado pelo hub IoT Edge nesse dispositivo.

Crie uma instância ModuleClient para ligar o seu módulo ao hub IoT Edge em funcionamento no dispositivo, semelhante à forma como as instâncias DeviceClient ligam dispositivos IoT ao IoT Hub. Para obter mais informações sobre a classe MóduloClient e seus métodos de comunicação, consulte a referência API para a sua língua SDK preferida: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C,](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient) [Java,](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)ou [Node.js](/javascript/api/azure-iot-device/moduleclient).

## <a name="language-and-architecture-support"></a>Apoio à linguagem e arquitetura

O IoT Edge suporta vários sistemas operativos, arquiteturas de dispositivos e linguagens de desenvolvimento para que possa construir o cenário que corresponda às suas necessidades. Utilize esta secção para compreender as suas opções para desenvolver módulos IoT Edge personalizados. Pode aprender mais sobre suporte de ferramentas e requisitos para cada idioma em [Preparar o seu ambiente de desenvolvimento e teste para ioT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todos os idiomas da tabela seguinte, o IoT Edge suporta o desenvolvimento de dispositivos AMD64 e ARM32 Linux.

| Linguagem de programação | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| C# | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O suporte de desenvolvimento e depuragem para dispositivos ARM64 Linux está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, consulte [os módulos Develop and debug ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Para todos os idiomas da tabela seguinte, o IoT Edge suporta o desenvolvimento de dispositivos AMD64 Windows.

| Linguagem de programação | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Estúdio Visual 2017/2019 |
| C# | Código do Estúdio Visual (sem capacidades de depuração)<br>Estúdio Visual 2017/2019 |

## <a name="next-steps"></a>Passos seguintes

[Prepare o seu ambiente de desenvolvimento e teste para ioT Edge](development-environment.md)

[Use o Estúdio Visual para desenvolver módulos C# para IoT Edge](how-to-visual-studio-develop-module.md)

[Use o Código do Estúdio Visual para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Compreender e utilizar os SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md)