---
title: Guia de desenvolvedores de serviços - IoT Plug and Play ! Microsoft Docs
description: Descrição de IoT Plug and Play para desenvolvedores de serviços
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e7728af831b26bff19f347e5b85db6420e7966ed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580478"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guia de desenvolvedores de serviço ioT plug e play

O IoT Plug and Play permite-lhe construir dispositivos inteligentes que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play.

O IoT Plug and Play permite-lhe utilizar dispositivos que anunciaram o seu ID do modelo com o seu hub IoT. Por exemplo, pode aceder diretamente às propriedades e comandos de um dispositivo.

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, utilize um dos SDKs de serviço IoT ou o IoT Hub REST API:

## <a name="service-sdks"></a>SDKs de Serviço

Utilize os SDKs de serviço Azure IoT na sua solução para interagir com dispositivos e módulos. Por exemplo, pode utilizar os SDKs de serviço para ler e atualizar propriedades gémeas e invocar comandos. As línguas apoiadas incluem C#, Java, Node.js e Python.

Os SDKs de serviço permitem-lhe aceder a informações do dispositivo a partir de uma solução, como um ambiente de trabalho ou uma aplicação web. Os SDKs de serviço incluem dois espaços de nome e modelos de objetos que pode usar para recuperar o ID do modelo:

- Cliente de serviço Iot Hub.
- Cliente de serviço digital Twins.

| Linguagem | Cliente de serviço IoT Hub | Cliente de serviço Digital Twins |
| -------- | ---------------------- | ---------------------------- |
| C#       | [Documentação](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Amostra](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples/Thermostat/Program.cs)| [Amostra](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Documentação](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample/thermostat-service-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/service/Thermostat.java)| [Amostra](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Documentação](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Documentação](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) |
| Python   | [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Amostra](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Documentação](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) | 

## <a name="rest-api"></a>API REST

Os exemplos a seguir utilizam a API IoT Hub REST para interagir com um dispositivo IoT Plug and Play ligado. A versão atual da API é `2020-09-30` . Apêndice `?api-version=2020-05-31` às suas chamadas DE REPOUSO PI.

> [!NOTE]
> Os gémeos módulos não são atualmente suportados pela `digitalTwins` API.

Se o seu dispositivo termóstato for `t-123` chamado, obtém todas as propriedades em todas as interfaces implementadas pelo seu dispositivo com uma chamada REST API GET:

```REST
GET /digitalTwins/t-123
```

Esta chamada incluirá a propriedade Json `$metadata.$model` com o ID do modelo anunciado pelo dispositivo.

Todas as propriedades em todas as interfaces são acedidas com o `GET /DigitalTwin/{device-id}` modelo DE API REST onde `{device-id}` está o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}
```

Pode ligar diretamente para os comandos do dispositivo IoT Plug and Play. Se o `Thermostat` componente do dispositivo tiver um `t-123` `restart` comando, pode chamá-lo com uma chamada REST API POST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

De uma forma mais geral, os comandos podem ser chamados através deste modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `component-name`: o nome da interface a partir da secção de implementações no modelo de capacidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Componentes do modelo](./concepts-components.md)
- [Instale e utilize as ferramentas de autoria DTDL](howto-use-dtdl-authoring-tools.md)
