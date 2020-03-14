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
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271385"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva os seus próprios módulos IoT Edge

Os módulos Azure IoT Edge podem ligar-se a outros serviços Do Azure e contribuir para o seu maior pipeline de dados em nuvem. Este artigo descreve como você pode desenvolver módulos para comunicar com o tempo de execução IoT Edge e IoT Hub, e, portanto, o resto da nuvem Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente de runtime do IoT Edge

O runtime do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implementá-las em dispositivos IoT Edge. Qualquer programa pode ser embalado como um módulo IoT Edge. Para tirar o máximo partido das funcionalidades de comunicação e gestão ioT Edge, um programa em execução num módulo pode utilizar o Dispositivo Azure IoT SDK para se ligar ao hub ioT Edge local.

## <a name="using-the-iot-edge-hub"></a>Utilizar o hub IoT Edge

O hub IoT Edge fornece duas funcionalidades principais: proxy para o IoT Hub e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos de IoT Hub

IoT Hub vê uma instância de módulo analogously para um dispositivo, no sentido de que:

* tem um módulo gémeo distinto e isolado do [dispositivo gémeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros gémeos módulos desse dispositivo;
* pode enviar [mensagens dispositivo-nuvem;](../iot-hub/iot-hub-devguide-messaging.md)
* pode receber [métodos diretos dirigidos](../iot-hub/iot-hub-devguide-direct-methods.md) especificamente à sua identidade.

Atualmente, os módulos não podem receber mensagens cloud-to-device ou usar a funcionalidade de upload de ficheiros.

Ao escrever um módulo, pode utilizar o [SDK do Dispositivo Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) para se ligar ao hub IoT Edge e utilizar a funcionalidade acima como faria ao utilizar o IoT Hub com uma aplicação de dispositivo. A única diferença entre os módulos IoT Edge e as aplicações do dispositivo IoT é que tem de se referir à identidade do módulo em vez da identidade do dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud

Para permitir o processamento complexo de mensagens dispositivo-cloud, o hub IoT Edge fornece o encaminhamento declarativo de mensagens entre módulos, e entre módulos e IoT Hub. Encaminhamento declarativa permite que os módulos interceptar e processar mensagens enviadas por outros módulos e propagação-las em pipelines de complexos. Para mais informações, consulte [módulos de implantação e estabeleça rotas em IoT Edge](module-composition.md).

Um módulo IoT Edge, ao contrário de uma aplicação normal do dispositivo IoT Hub, pode receber mensagens dispositivo-nuvem que estão a ser procuradas pelo seu hub ioT edge local para processá-las.

O hub IoT Edge propaga as mensagens para o seu módulo com base em rotas declarativas descritas no manifesto de [implantação](module-composition.md). Ao desenvolver um módulo do IoT Edge, pode receber essas mensagens através da definição de manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge adiciona o conceito de *entrada* de módulos e pontos finais de *saída.* Um módulo pode receber todas as mensagens de dispositivo-para-cloud encaminhadas para o mesmo sem especificar qualquer entrada e pode enviar mensagens dispositivo-para a cloud sem especificar quaisquer dados. No entanto, uso explícitas entradas e saídas, torna as regras de roteamento mais simples de entender.

Por fim, as mensagens de dispositivo-para-cloud manipuladas pelo Edge hub são marcadas com as seguintes propriedades do sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | O ID de dispositivo do cliente que enviou a mensagem |
| $connectionModuleId | O ID de módulo do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode estar vazio. |
| $outputName | A saída utilizada para enviar a mensagem. Pode estar vazio. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligar ao hub IoT Edge, a partir de um módulo

Ligar ao hub IoT Edge local a partir de um módulo envolve dois passos:

1. Crie uma instância De MóduloCliente na sua aplicação.
2. Certifique-se de que seu aplicativo aceita o certificado apresentado pelo hub IoT Edge nesse dispositivo.

Crie uma instância ModuleClient para ligar o seu módulo ao hub IoT Edge em execução no dispositivo, semelhante à forma como as instâncias do DeviceClient ligam dispositivos IoT ao IoT Hub. Para obter mais informações sobre a classe ModuleClient e os seus métodos [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)de comunicação, consulte a referência API para a sua língua SDK preferida: [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)ou [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Apoio à linguagem e arquitetura

O IoT Edge suporta múltiplos sistemas operativos, arquiteturas de dispositivos e linguagens de desenvolvimento para que possa construir o cenário que corresponda às suas necessidades. Utilize esta secção para entender as suas opções para desenvolver módulos IoT Edge personalizados. Você pode aprender mais sobre suporte de ferramentas e requisitos para cada idioma em Preparar o seu ambiente de [desenvolvimento e teste para IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todas as línguas na tabela seguinte, o IoT Edge suporta o desenvolvimento para dispositivos LINUx AMD64 e ARM32.

| Linguagem de desenvolvimento | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| C# | Visual Studio Code<br>Estúdio Visual 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O suporte de desenvolvimento e depuração para dispositivos ARM64 Linux está em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para mais informações, consulte [Desenvolver e depurar módulos ARM64 IoT Edge no Código do Estúdio Visual (pré-visualização)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Para todos os idiomas na tabela a seguir, o IoT Edge suporta o desenvolvimento de dispositivos AmD64 Windows.

| Linguagem de desenvolvimento | Ferramentas de programação |
| -------------------- | ----------------- |
| C | Estúdio Visual 2017/2019 |
| C# | Código de Estúdio Visual (sem capacidades de depuração)<br>Estúdio Visual 2017/2019 |

## <a name="next-steps"></a>Passos seguintes

[Prepare o seu ambiente de desenvolvimento e teste para ioT Edge](development-environment.md)

[Use o Estúdio C# Visual para desenvolver módulos para IoT Edge](how-to-visual-studio-develop-module.md)

[Use o Código do Estúdio Visual para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Compreenda e use Os DSDs do Hub Azure IoT](../iot-hub/iot-hub-devguide-sdks.md)
