---
title: Entender os SDKs do Azure IoT | Microsoft Docs
description: Guia do desenvolvedor-informações e links para os vários SDKs de serviço e dispositivo IoT do Azure que você pode usar para criar aplicativos de dispositivo e aplicativos de back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 419a71acbca59c00bd0ffecf39d5d1ff38833b08
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045145"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Entender e usar SDKs do Hub IoT do Azure

Há duas categorias de SDKs (kits de desenvolvimento de software) para trabalhar com o Hub IoT:

* Os **SDKs de dispositivo do Hub IOT** permitem que você crie aplicativos que são executados em seus dispositivos IOT usando o cliente do dispositivo ou o cliente do módulo. Estas aplicações enviam telemetria para o Hub IoT e, opcionalmente, recebem mensagens, tarefas, métodos ou atualizações de duplos do hub IoT.  Você também pode usar o cliente de módulo para criar [módulos](../iot-edge/iot-edge-modules.md) para [Azure IOT Edge tempo de execução](../iot-edge/about-iot-edge.md).

* Os **SDKs de serviço do Hub IOT** permitem criar aplicativos de back-end para gerenciar o Hub IOT e, opcionalmente, enviar mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedades desejadas para seus dispositivos ou módulos IOT.

Além disso, também fornecemos um conjunto de SDKs para trabalhar com o [serviço de provisionamento de dispositivos](../iot-dps/about-iot-dps.md).
* O **provisionamento de SDKs de dispositivo** permite que você crie aplicativos que são executados em seus dispositivos IOT para se comunicar com o serviço de provisionamento de dispositivos.

* Os **SDKs de serviço de provisionamento** permitem que você crie aplicativos de back-end para gerenciar seus registros no serviço de provisionamento de dispositivos.

Saiba mais sobre os [benefícios do desenvolvimento usando SDKs de IOT do Azure](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma e hardware do so

As plataformas com suporte para os SDKs podem ser encontradas no [suporte à plataforma de SDKs do Azure IOT](iot-hub-device-sdk-platform-support.md).

Para obter mais informações sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com/) ou repositório individual.

## <a name="azure-iot-hub-device-sdks"></a>SDKs de dispositivo do Hub IoT do Azure

O Microsoft Azure SDKs do dispositivo IoT contêm código que facilita a criação de aplicativos que se conectam ao e são gerenciados pelos serviços do Hub IoT do Azure.

SDK do dispositivo do Hub IoT do Azure para .NET: 

* Baixar do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  O namespace é Microsoft. Azure. Devices. clients, que contém clientes de dispositivo do Hub IoT (DeviceClient, ModuleClient).
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência de módulo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

SDK do dispositivo do Hub IoT do Azure para C (ANSI C-C99):

* Instalar do [apt-get, MBED, ARDUINO IDE ou Ios](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c)
* [Compilar o SDK do dispositivo C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Referência de módulo](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portando o SDK do C para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do desenvolvedor](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para obter informações sobre compilação cruzada, introdução em diferentes plataformas, etc.
* [Informações de consumo de recursos do SDK C do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

SDK do dispositivo do Hub IoT do Azure para Java: 

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência de módulo](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

SDK do dispositivo do Hub IoT do Azure para node. js: 

* Instalar do [NPM](https://www.npmjs.com/package/azure-iot-device)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência de módulo](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

SDK do dispositivo do Hub IoT do Azure para Python: 

* Instalar do [Pip](https://pypi.org/project/azure-iot-device/)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-python)
* [Referência da API](https://docs.microsoft.com/python/api/azure-iot-device)

SDK do dispositivo do Hub IoT do Azure para iOS: 

* Instalar do [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência de API: consulte [referência da API do C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDKs de serviço do Hub IoT do Azure

Os SDKs do serviço IoT do Azure contêm código para facilitar a criação de aplicativos que interagem diretamente com o Hub IoT para gerenciar dispositivos e segurança.

SDK do serviço do Hub IoT do Azure para .NET:

* Baixar do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  O namespace é Microsoft. Azure. Devices, que contém clientes de serviço do Hub IoT (Registrymanager, ServiceName).
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

SDK do serviço de Hub IoT do Azure para Java: 

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

SDK de serviço do Hub IoT do Azure para node. js: 

* Baixar de [NPM](https://www.npmjs.com/package/azure-iothub)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

SDK do serviço de Hub IoT do Azure para Python: 

* Baixar do [Pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-python/tree/master)

SDK do serviço de Hub IoT do Azure para C: 

* Baixar de [apt-get, MBED, ARDUINO IDE ou NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c)

SDK do serviço do Hub IoT do Azure para iOS: 

* Instalar do [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios do GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos da plataforma e da linguagem para instalar binários e dependências em seu computador de desenvolvimento.

## <a name="microsoft-azure-provisioning-sdks"></a>SDKs de provisionamento do Microsoft Azure

Os **SDKs de provisionamento do Microsoft Azure** permitem provisionar dispositivos para o Hub IOT usando o [serviço de provisionamento de dispositivos](../iot-dps/about-iot-dps.md).

Dispositivos de provisionamento do Azure e SDKs de serviço C#para:

* Baixe do [SDK do dispositivo](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e do [SDK do serviço](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) do NuGet.
* [Código fonte](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Dispositivos de provisionamento do Azure e SDKs de serviço para C:

* Instalar do [apt-get, MBED, ARDUINO IDE ou Ios](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Dispositivo de provisionamento do Azure e SDKs de serviço para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código fonte](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Dispositivo de provisionamento do Azure e SDKs de serviço para node. js:

* [Código fonte](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referência da API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Baixar o [SDK do dispositivo](https://badge.fury.io/js/azure-iot-provisioning-device) e o SDK do [serviço](https://badge.fury.io/js/azure-iot-provisioning-service) em NPM

SDK de serviço e dispositivo de provisionamento do Azure para Python:

* [Código fonte](https://github.com/Azure/azure-iot-device)
* Baixar o [SDK do dispositivo](https://pypi.org/project/azure-iot-device/) e o SDK do [serviço](https://pypi.org/project/azure-iothub-provisioningserviceclient/) do Pip

## <a name="next-steps"></a>Passos seguintes

Os SDKs do Azure IoT também fornecem um conjunto de ferramentas para ajudar no desenvolvimento:
* [iothub-Diagnostics](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comando de plataforma cruzada para ajudar a diagnosticar problemas relacionados à conexão com o Hub IOT.
* [Azure-IOT-Explorer](https://github.com/Azure/azure-iot-explorer): um aplicativo de área de trabalho de plataforma cruzada para se conectar ao Hub IOT e adicionar/gerenciar/se comunicar com dispositivos IOT.

Documentos relevantes relacionados ao desenvolvimento usando os SDKs de IoT do Azure:
* Saiba mais sobre [como gerenciar a conectividade e as mensagens confiáveis](iot-hub-reliability-features-in-sdks.md) usando os SDKs do Hub IOT.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) , como Ios e Android.
* [Suporte à plataforma SDK do Azure IoT](iot-hub-device-sdk-platform-support.md)


Outros tópicos de referência neste guia do desenvolvedor do Hub IoT incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte a MQTT do Hub IoT](iot-hub-mqtt-support.md)
* [Referência da API REST do Hub IoT](/rest/api/iothub/)
