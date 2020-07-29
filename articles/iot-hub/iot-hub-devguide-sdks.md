---
title: Compreenda os Azure IoT SDKs ! Microsoft Docs
description: Guia do desenvolvedor - informações sobre e links para os vários dispositivos EDKs de serviço Azure IoT que você pode usar para construir aplicações de dispositivos e aplicações de back-end.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 1933a53b6e69a6179faaa0bd6b86619478159b3b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322929"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Compreender e utilizar os SDKs do Hub IoT do Azure

Existem duas categorias de kits de desenvolvimento de software (SDKs) para trabalhar com o IoT Hub:

* **Os SDKs de dispositivo ioT Hub** permitem-lhe construir aplicações que funcionam nos seus dispositivos IoT utilizando o cliente do dispositivo ou cliente do módulo. Estas aplicações enviam telemetria para o Hub IoT e, opcionalmente, recebem mensagens, tarefas, métodos ou atualizações de duplos do hub IoT.  Também pode utilizar o cliente do módulo para os [módulos](../iot-edge/iot-edge-modules.md) de autor para [o tempo de execução Azure IoT Edge.](../iot-edge/about-iot-edge.md)

* **Os SDKs ioT Hub Service** permitem-lhe construir aplicações backend para gerir o seu hub IoT e enviar opcionalmente mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedade desejadas para os seus dispositivos ou módulos IoT.

Além disso, também fornecemos um conjunto de SDKs para trabalhar com o [Serviço de Provisionamento de Dispositivos.](../iot-dps/about-iot-dps.md)

* **O fornecimento de SDKs de dispositivo permite-lhe** construir aplicações que funcionam nos seus dispositivos IoT para comunicar com o Serviço de Provisionamento de Dispositivos.

* **Os SDKs de serviço** de fornecimento permitem-lhe construir aplicações de backend para gerir as suas inscrições no Serviço de Provisionamento de Dispositivos.

Conheça os [benefícios de desenvolver usando Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Plataforma de SO e compatibilidade de hardware

Plataformas suportadas para os SDKs podem ser encontradas no [Suporte à Plataforma Azure IoT SDKs](iot-hub-device-sdk-platform-support.md).

Para obter mais informações sobre a compatibilidade da SDK com dispositivos de hardware específicos, consulte o catálogo de [dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com/) ou o repositório individual.

## <a name="azure-iot-hub-device-sdks"></a>SDKs de dispositivo de hub Azure IoT

Os SDKs de dispositivo Microsoft Azure IoT contêm código que facilita a construção de aplicações que se ligam e são geridas pelos serviços Azure IoT Hub.

Dispositivo Azure IoT Hub SDK para .NET: 

* Download a partir de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  O espaço de nomes é Microsoft.Azure.Devices.Clients, que contém clientes de dispositivos IoT Hub (DeviceClient, ModuleClient).
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência do módulo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Dispositivo Azure IoT Hub SDK para C (ANSI C - C99):

* Instalação a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c)
* [Compilar o Dispositivo C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Referência do módulo](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portando o C SDK para outras plataformas](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentação do programador](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) para informação sobre compilação cruzada, iniciando-se em diferentes plataformas, etc.
* [Informação sobre o consumo de recursos Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Dispositivo Azure IoT Hub SDK para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência do módulo](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Dispositivo Azure IoT Hub SDK para Node.js:

* Instalar a partir de [npm](https://www.npmjs.com/package/azure-iot-device)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência do módulo](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Dispositivo Azure IoT Hub SDK para Python:

* Instalação a partir de [pip](https://pypi.org/project/azure-iot-device/)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-python)
* [Referência da API](https://docs.microsoft.com/python/api/azure-iot-device)

Dispositivo Azure IoT Hub SDK para iOS:

* Instalação a partir de [Cacau](https://cocoapods.org/pods/AzureIoTHubClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência API: ver [referência C API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDKs de serviço Azure IoT Hub

Os SDKs de serviço Azure IoT contêm código para facilitar aplicações de construção que interagem diretamente com o IoT Hub para gerir dispositivos e segurança.

Serviço Azure IoT Hub SDK para .NET:

* Download a partir de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  O espaço de nomes é Microsoft.Azure.Devices, que contém clientes de serviço ioT hub (RegistryManager, ServiceClients).
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Serviço Azure IoT Hub SDK para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Serviço Azure IoT Hub SDK para Node.js:

* Baixar a partir de [npm](https://www.npmjs.com/package/azure-iothub)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-node)
* [Referência da API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Serviço Azure IoT Hub SDK para Python:

* Download a partir de [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [Referência da API](https://docs.microsoft.com/python/api/azure-iot-hub)

Serviço Azure IoT Hub SDK para C:

O Azure IoT Service SDK para C já não está em desenvolvimento ativo.
Continuaremos a corrigir bugs críticos como falhas, corrupção de dados e vulnerabilidades de segurança. No entanto, NÃO adicionaremos qualquer nova funcionalidade ou correção de bugs que não sejam críticos.

O suporte SDK do Serviço Azure IoT está disponível em idiomas de nível superior[(C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java,](https://github.com/Azure/azure-iot-sdk-java) [Node,](https://github.com/Azure/azure-iot-sdk-node) [Python).](https://github.com/Azure/azure-iot-sdk-python)

* Download de [apt-get, MBED, Arduino IDE ou NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c)

Serviço Azure IoT Hub SDK para iOS:

* Instalação a partir de [Cacau](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Amostras](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os ficheiros de readme nos repositórios do GitHub para obter informações sobre a utilização de gestores de pacotes específicos para a linguagem e plataforma para instalar binários e dependências na sua máquina de desenvolvimento.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure Provisioning SDKs

Os **SDKs de Provisionamento do Microsoft Azure** permitem-lhe a provisionar dispositivos ao seu Hub IoT utilizando o [Serviço de Provisionamento de Dispositivos](../iot-dps/about-iot-dps.md).

Dispositivo de provisão Azure e SDKs de serviço para C#:

* Descarregue a partir de [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) da NuGet.
* [Código de origem](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Referência da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Dispositivo de fornecimento de Azure e SDKs de serviço para C:

* Instalação a partir de [apt-get, MBED, Arduino IDE ou iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Referência da API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Dispositivo de fornecimento de Azure e SDKs de serviço para Java:

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Código de origem](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Referência da API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Dispositivo de fornecimento de Azure e SDKs de serviço para Node.js:

* [Código de origem](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Referência da API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* [Descarregar dispositivo SDK](https://badge.fury.io/js/azure-iot-provisioning-device) e [Serviço SDK](https://badge.fury.io/js/azure-iot-provisioning-service) a partir de npm

Dispositivo de provisão Azure e SDKs de serviço para Python:

* [Código de origem](https://github.com/Azure/azure-iot-sdk-python)
* [Descarregar dispositivo SDK](https://pypi.org/project/azure-iot-device/) e [Serviço SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) do pip

## <a name="next-steps"></a>Passos seguintes

Os Azure IoT SDKs também fornecem um conjunto de ferramentas para ajudar no desenvolvimento:

* [iothub-diagnósticos](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comando transversal para ajudar a diagnosticar problemas relacionados com a ligação com o IoT Hub.
* [azure-iot-explorer:](https://github.com/Azure/azure-iot-explorer)uma aplicação de ambiente de trabalho de plataforma cruzada para ligar ao seu IoT Hub e adicionar/gerir/comunicar com dispositivos IoT.

Documentos relevantes relacionados com o desenvolvimento utilizando os SDKs Azure IoT:

* Saiba [como gerir a conectividade e mensagens fiáveis](iot-hub-reliability-features-in-sdks.md) usando os IoT Hub SDKs.
* Saiba como [se desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte à plataforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

Outros tópicos de referência neste guia de desenvolvimento do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
* [IoT Hub linguagem de consulta para gémeos de dispositivo, empregos e encaminhamento de mensagens](iot-hub-devguide-query-language.md)
* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Referência API IoT Hub REST](/rest/api/iothub/)
