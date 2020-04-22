---
title: Compreenda os DSDs Azure IoT [ Microsoft Docs
description: Guia de desenvolvedores - informações sobre e links para os vários sDKs de dispositivo e serviço Azure IoT que você pode usar para construir aplicações de dispositivos e aplicações back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom: mqtt
ms.openlocfilehash: 68186e777051e77deca7b6e5891eb392cac23260
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729041"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Compreenda e use Os DSDs do Hub Azure IoT

Existem duas categorias de kits de desenvolvimento de software (SDKs) para trabalhar com o IoT Hub:

* **Os SDKs do IoT Hub Device** permitem-lhe construir aplicações que funcionam nos seus dispositivos IoT utilizando cliente de dispositivoou cliente de módulos. Estas aplicações enviam telemetria para o Hub IoT e, opcionalmente, recebem mensagens, tarefas, métodos ou atualizações de duplos do hub IoT.  Também pode utilizar o cliente do módulo para [autor de módulos](../iot-edge/iot-edge-modules.md) para o tempo de [funcionação do Azure IoT Edge.](../iot-edge/about-iot-edge.md)

* **Os SDKs de Serviço IoT Hub** permitem-lhe construir aplicações de backend para gerir o seu hub IoT e, opcionalmente, enviar mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedade desejadas para os seus dispositivos ou módulos IoT.

Além disso, também fornecemos um conjunto de SDKs para trabalhar com o Serviço de Provisionamento de [Dispositivos](../iot-dps/about-iot-dps.md).
* **Os SDKs** de fornecimento permitem-lhe construir aplicações que funcionam nos seus dispositivos IoT para comunicar com o Serviço de Provisionamento de Dispositivos.

* Os **SDKs do Serviço** de Provisionamento permitem-lhe construir aplicações de backend para gerir as suas matrículas no Serviço de Provisionamento de Dispositivos.

Conheça os [benefícios do desenvolvimento utilizando DSKs Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade da plataforma e hardware da plataforma e do hardware

Plataformas suportadas para os SDKs podem ser encontradas no Suporte à [Plataforma SDKs Azure IoT](iot-hub-device-sdk-platform-support.md).

Para obter mais informações sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de Dispositivos Azure Certified para catálogo de dispositivos IoT](https://catalog.azureiotsolutions.com/) ou repositório individual.

## <a name="azure-iot-hub-device-sdks"></a>SDKs de dispositivo sdKs do hub Azure IoT

Os SDKs do dispositivo Microsoft Azure IoT contêm código que facilita a construção de aplicações que se ligam e são geridas pelos serviços Do Hub Azure IoT.

Dispositivo Hub Azure IoT SDK para .NET: 

* Baixar a partir de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  O espaço de nome é Microsoft.Azure.Devices.Clients, que contém clientes de dispositivos IoT Hub (DeviceClient, ModuleClient).
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência do módulo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Dispositivo Hub Azure IoT SDK para C (ANSI C - C99):

* Instalar a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c)
* [Compilar o Dispositivo C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Referência do módulo](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Porting o C SDK para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do desenvolvedor](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para informações sobre compilação cruzada, início em diferentes plataformas, etc.
* [Informação sobre consumo de recursos azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Dispositivo Hub Azure IoT SDK para Java: 

* Adicione ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência do módulo](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Dispositivo Hub Azure IoT SDK para Node.js: 

* Instalar a partir das [12h](https://www.npmjs.com/package/azure-iot-device)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência do módulo](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Dispositivo Hub Azure IoT SDK para Python: 

* Instalar a partir de [pip](https://pypi.org/project/azure-iot-device/)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-python)
* [Referência da API](https://docs.microsoft.com/python/api/azure-iot-device)

Dispositivo Hub Azure IoT SDK para iOS: 

* Instalação a partir de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência API: ver [referência C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDKs de serviço azure ioT hub

Os SDKs do serviço Azure IoT contêm código para facilitar aplicações de construção que interagem diretamente com o IoT Hub para gerir dispositivos e segurança.

Serviço Azure IoT Hub SDK para .NET:

* Baixar a partir de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  O espaço de nome é Microsoft.Azure.Devices, que contém Clientes de Serviço IoT Hub (RegistryManager, ServiceClients).
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Serviço Azure IoT Hub SDK para Java: 

* Adicione ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Serviço Azure IoT Hub SDK para Node.js: 

* Baixar a partir do [dia 22](https://www.npmjs.com/package/azure-iothub)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Serviço Azure IoT Hub SDK para Python: 

* Baixar a partir de [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Serviço Azure IoT Hub SDK para C: 

* Baixar de [apt-get, MBED, Arduino IDE ou NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c)

Serviço Azure IoT Hub SDK para iOS: 

* Instalação a partir de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os ficheiros readme nos repositórios GitHub para obter informações sobre a utilização de gestores de pacotes específicos de linguagem e plataforma para instalar binários e dependências na sua máquina de desenvolvimento.

## <a name="microsoft-azure-provisioning-sdks"></a>SDKs de provisionamento Microsoft Azure

Os **SDKs de Provisionamento Microsoft Azure** permitem-lhe fornecer dispositivos ao seu Hub IoT utilizando o Serviço de Provisionamento de [Dispositivos](../iot-dps/about-iot-dps.md).

Dispositivo de provisionamento Azure e SDKs de serviço para C#:

* Baixar a partir de [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) a partir de NuGet.
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Dispositivo de provisionamento Azure e SDKs de serviço para C:

* Instalar a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Dispositivo de provisionamento Azure e SDKs de serviço para Java:

* Adicione ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Dispositivo de provisionamento Azure e SDKs de serviço para Node.js:

* [Código de origem](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referência da API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Descarregue [o Dispositivo SDK](https://badge.fury.io/js/azure-iot-provisioning-device) e [o Serviço SDK](https://badge.fury.io/js/azure-iot-provisioning-service) a partir das 12h

Dispositivo de provisionamento Azure e SDKs de serviço para Python:

* [Código de origem](https://github.com/Azure/azure-iot-sdk-python)
* Descarregue [o Dispositivo SDK](https://pypi.org/project/azure-iot-device/) e [o Serviço SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) a partir do pip

## <a name="next-steps"></a>Passos seguintes

Os SDKs Azure IoT também fornecem um conjunto de ferramentas para ajudar no desenvolvimento:
* [Iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comando transversal para ajudar a diagnosticar problemas relacionados com a ligação com o IoT Hub.
* [azure-iot-explorer](https://github.com/Azure/azure-iot-explorer): uma aplicação de ambiente de trabalho transversal para ligar ao seu Hub IoT e adicionar/gerir/comunicar com dispositivos IoT.

Docs relevantes relacionados com o desenvolvimento utilizando os DSTS Azure IoT:
* Saiba [como gerir a conectividade e mensagens fiáveis](iot-hub-reliability-features-in-sdks.md) utilizando os SDKs IoT Hub.
* Saiba como [se desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte à plataforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md)


Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
* [IoT Hub consulta linguagem para gémeos dispositivo, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Referência IoT Hub REST API](/rest/api/iothub/)
