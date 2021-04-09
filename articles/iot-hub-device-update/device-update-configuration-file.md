---
title: Compreenda a atualização do dispositivo para o ficheiro de configuração do hub Azure IoT| Microsoft Docs
description: Compreenda a atualização do dispositivo para o ficheiro de configuração do hub IoT Azure.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662947"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Atualização do dispositivo para ficheiro de configuração do hub IoT

O "adu-conf.txt" é um ficheiro opcional que pode ser criado para gerir as seguintes configurações.

* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["fabricante"]
* AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["modelo"]
* DeviceInformation.fabricante
* DeviceInformation.modelo
* Cadeia de ligação do dispositivo (se não for conhecida pelo agente de atualização do dispositivo).

## <a name="purpose"></a>Objetivo
O Agente de Atualização do Dispositivo tentará primeiro obter os `manufacturer` valores e `model` valores do dispositivo para utilizar para a [Camada de Interface](device-update-agent-overview.md#the-interface-layer). Se isso falhar, o Agente de Atualização do Dispositivo procurará em seguida o ficheiro "adu-conf.txt" e utilizará os valores a partir daí. Se ambas as tentativas não forem bem sucedidas, o Agente de Atualização do Dispositivo utilizará valores [predefinidos.](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt)

Saiba mais sobre [a Interface de Interface Do Núcleo da ADU](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) e interface de [informação do dispositivo.](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)

## <a name="file-location"></a>Localização do arquivo

Dentro do sistema Linux, na partição ou disco chamado `adu` , crie um ficheiro de texto chamado "adu-conf.txt" com os seguintes campos.

## <a name="list-of-fields"></a>Lista de campos

|Nome|Descrição|
|-----------|--------------------|
|connection_string|Cadeia de ligação pré-a provisionada que o dispositivo pode utilizar para ligar ao Hub IoT. Nota: Não é necessário se estiver a fortar o Agente de Atualização do Dispositivo através do [Serviço de Identidade Azure IoT](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Reportado pela `AzureDeviceUpdateCore:4.ClientMetadata:4` interface para classificar o dispositivo para direcionar a implementação da atualização.|
|aduc_model|Reportado pela `AzureDeviceUpdateCore:4.ClientMetadata:4` interface para classificar o dispositivo para direcionar a implementação da atualização.|
|manufacturer|Reportado pelo Agente de Atualização do Dispositivo como parte da `DeviceInformation` interface.|
|model|Reportado pelo Agente de Atualização do Dispositivo como parte da `DeviceInformation` interface.|

## <a name="example-adu-conftxt-file-contents"></a>Exemplo de conteúdo de ficheiros "adu-conf.txt"

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
