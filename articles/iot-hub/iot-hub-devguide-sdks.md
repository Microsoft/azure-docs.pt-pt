---
title: Compreender os SDKs IoT do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – informações sobre e links para os vários Azure IoT dispositivo e o serviço de SDKs que pode utilizar para criar aplicações de dispositivos e aplicações de back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: fcb6f564c97632e4d62f7f052ec5ecc35a9e479a
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011250"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Compreender e utilizar os SDKs do IoT Hub do Azure

Existem duas categorias de kits de desenvolvimento de software (SDKs) para trabalhar com o IoT Hub:

* **SDKs de dispositivo do IoT Hub** permitem-lhe criar aplicações que são executadas nos seus dispositivos de IoT com o cliente do dispositivo ou o cliente do módulo. Estas aplicações enviam telemetria ao seu hub IoT e, opcionalmente, recebem mensagens, tarefa, método ou atualizações de duplo do seu hub IoT.  Também pode usar o cliente do módulo para criar [módulos](../iot-edge/iot-edge-modules.md) para [runtime do Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **SDKs de serviço do Hub IoT** permitem-lhe criar aplicações de back-end para gerir o seu hub IoT e, opcionalmente, enviar mensagens, agenda tarefas, invoca métodos diretos ou enviar atualizações de propriedade pretendida para seus dispositivos IoT ou módulos.

Além disso, também disponibilizamos um conjunto de SDKs para trabalhar com o [serviço aprovisionamento de dispositivos](../iot-dps/about-iot-dps.md).
* **SDKs de dispositivo de aprovisionamento** permitem-lhe criar aplicações que são executadas nos seus dispositivos de IoT para comunicar com o serviço de aprovisionamento de dispositivos.

* **SDKs de serviço de aprovisionamento** permitem-lhe criar aplicações de back-end para gerir as inscrições no serviço de aprovisionamento de dispositivos.

Saiba mais sobre o [benefícios da programação com o Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma e de hardware do sistema operacional

Plataformas suportadas para os SDKs que podem ser encontradas na [suporte de plataforma do Azure IoT SDKs](iot-hub-device-sdk-platform-support.md).

Para obter mais informações sobre a compatibilidade de SDK com dispositivos de hardware específicas, consulte a [certificação do Azure para o catálogo de dispositivos de IoT](https://catalog.azureiotsolutions.com/) ou repositório individual.

## <a name="azure-iot-hub-device-sdks"></a>SDKs de dispositivo do Hub IoT do Azure

Microsoft Azure IoT device SDKs contêm código que facilita a criação de aplicativos que se ligam para e é geridas pelos serviços do IoT Hub do Azure.

O Azure IoT Hub device SDK para .NET: 

* Transferir a partir [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  O espaço de nomes é Microsoft.Azure.Devices.Clients, que contém clientes de dispositivos de Hub IoT (DeviceClient, ModuleClient).
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência do módulo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

O Azure IoT Hub device SDK para C (ANSI C - C99):

* Instalar a partir do [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c)
* [Compilar o SDK de dispositivo C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Referência do módulo](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Transferir o SDK de C para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação de programador](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para obter informações sobre entre-compilar, introdução em diferentes plataformas, etc.

O Azure IoT Hub device SDK para Java: 

* Adicionar ao [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projeto
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência do módulo](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

O Azure IoT Hub device SDK para node. js: 

* Instalar a partir do [npm](https://www.npmjs.com/package/azure-iot-device)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência do módulo](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

O Azure IoT Hub device SDK para Python: 

* Instalar a partir do [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-python)
* Referência da API: veja [referência da API de C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

O Azure IoT Hub device SDK para iOS: 

* Instalar a partir do [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência da API: veja [referência da API de C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDKs de serviço do Hub IoT do Azure

Os SDKs de serviço de IoT do Azure contêm código para facilitar a criação de aplicativos que interagem diretamente com o IoT Hub para gerir dispositivos e segurança.

Serviço do Azure IoT Hub SDK para .NET:

* Transferir a partir [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  O espaço de nomes é Microsoft.Azure.Devices, que contém clientes de serviço de Hub IoT (RegistryManager, ServiceClients).
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Serviço do Azure IoT Hub SDK para Java: 

* Adicionar ao [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projeto
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Serviço do Azure IoT Hub SDK para node. js: 

* Transferir a partir do [npm](https://www.npmjs.com/package/azure-iothub)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Serviço do Azure IoT Hub SDK para Python: 

* Transferir a partir do [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-python)

Serviço IoT Hub do Azure SDK para c: 

* Transferir a partir do [apt-get, MBED, Arduino IDE ou Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c)

Serviço do Azure IoT Hub SDK para iOS: 

* Instalar a partir do [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os ficheiros Leia-me nos repositórios do GitHub para obter informações sobre como utilizar o idioma e gestores de pacotes específicos da plataforma para instalar os binários e dependências no computador de desenvolvimento.

## <a name="microsoft-azure-provisioning-sdks"></a>SDKs de aprovisionamento do Microsoft Azure

O **SDKs de aprovisionamento do Microsoft Azure** permite-lhe aprovisionar dispositivos no seu IoT Hub com o [serviço aprovisionamento de dispositivos](../iot-dps/about-iot-dps.md).

Aprovisionamento dispositivos e de serviços os SDKs do Azure para c#:

* Transferir a partir [SDK de dispositivo](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e [o SDK do serviço](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) do NuGet.
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Aprovisionamento dispositivos e de serviços os SDKs do Azure para c:

* Instalar a partir do [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Aprovisionamento dispositivos e de serviços os SDKs do Azure para Java:

* Adicionar ao [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projeto
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Aprovisionamento dispositivos e de serviços os SDKs do Azure para node. js:

* [Código fonte](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referência da API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Baixe [SDK de dispositivo](https://badge.fury.io/js/azure-iot-provisioning-device) e [SDK do serviço](https://badge.fury.io/js/azure-iot-provisioning-service) partir do npm

Aprovisionamento dispositivos e de serviços os SDKs do Azure para Python:

* [Código fonte](https://github.com/Azure/azure-iot-sdk-python)
* Baixe [SDK de dispositivo](https://pypi.org/project/azure-iot-provisioning-device-client/) e [SDK do serviço](https://pypi.org/project/azure-iothub-provisioningserviceclient/) do pip

## <a name="next-steps"></a>Passos Seguintes

Os SDKs IoT do Azure também fornecem um conjunto de ferramentas para ajudá-lo desenvolvimento:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comandos para várias plataformas para ajudar a diagnosticar problemas relacionados com a ligação com o IoT Hub.
* [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): um aplicativo de desktop do Windows para ligar ao seu IoT Hub.

Docs relevantes relacionados ao desenvolvimento com os SDKs IoT do Azure:
* Saiba mais sobre [como gerir o sistema de mensagens confiável e conectividade](iot-hub-reliability-features-in-sdks.md) com os SDKs do IoT Hub.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte de plataforma do SDK do IoT do Azure](iot-hub-device-sdk-platform-support.md)


Outros tópicos de referência neste guia de programador do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
* [Linguagem de consulta do IoT Hub para dispositivos duplos, tarefas e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte para MQTT de Hub IoT](iot-hub-mqtt-support.md)
* [Referência da API de REST do Hub IoT](/rest/api/iothub/)
