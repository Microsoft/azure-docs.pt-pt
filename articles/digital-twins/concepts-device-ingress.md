---
title: Conectividade do dispositivo e entrada de telemetria - Azure Digital Twins / Microsoft Docs
description: Aprenda a conectar, a bordo e envie telemetria a partir de um dispositivo IoT em Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75862480"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Entrada de conectividade e telemetria de dispositivo

Os dados de telemetria enviados por dispositivos e sensores formam a espinha dorsal de qualquer solução IoT. Como representar estes diferentes recursos e geri-los no contexto de um local são as principais preocupações no desenvolvimento de aplicações IoT. A Azure Digital Twins simplifica o processo de desenvolvimento de soluções IoT unindo dispositivos e sensores com um gráfico de inteligência espacial.

Para começar, crie um recurso Azure IoT Hub na raiz do gráfico espacial. O recurso IoT Hub permite que todos os dispositivos sob o espaço radicular enviem mensagens. Após a criação do IoT Hub, registe os dispositivos com sensores dentro da instância Das Gémeas Digitais. Os dispositivos podem enviar dados para um serviço Digital Twins através do [dispositivo Azure IoT SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Para obter um guia passo a passo sobre como trazer dispositivos a bordo, leia o [Tutorial para implementar e configurar As Gémeas Digitais](tutorial-facilities-setup.md). Num ápice, os passos são:

- Implemente uma instância de Gémeos Digitais a partir do [portal Azure.](https://portal.azure.com)
- Crie espaços no seu gráfico.
- Crie um recurso IoT Hub e atribua-o a um espaço no seu gráfico.
- Crie dispositivos e sensores no seu gráfico e atribua-os aos espaços criados nos passos anteriores.
- Crie um matcher para filtrar mensagens de telemetria com base nas condições.
- Crie uma [função definida pelo utilizador](concepts-user-defined-functions.md)e atribua-a a um espaço no gráfico para processamento personalizado das suas mensagens de telemetria.
- Atribuir uma função para permitir que a função definida pelo utilizador aceda aos dados do gráfico.
- Obtenha a cadeia de ligação do dispositivo IoT Hub das APIs de Gestão de Gémeos Digitais.
- Configure a cadeia de ligação do dispositivo no dispositivo com o dispositivo Azure IoT SDK.

Nas seguintes secções, aprende-se a obter a cadeia de ligação do dispositivo IoT Hub da API de Gestão de Gémeos Digitais. Também aprende a usar o formato de mensagem de telemetria IoT Hub para enviar telemetria baseada em sensores. As Gémeas Digitais exigem que cada peça de telemetria que recebe seja associada a um sensor dentro do gráfico espacial. Este requisito garante que os dados são processados e encaminhados dentro do contexto espacial adequado.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obtenha a cadeia de ligação do dispositivo IoT Hub da API de Gestão

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Faça uma chamada GET na API do dispositivo com um `includes=ConnectionString` parâmetro para obter a cadeia de ligação do dispositivo IoT Hub. Filtrar pelo dispositivo GUID ou pelo ID do hardware para encontrar o dispositivo.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_GUID* | O ID do dispositivo |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valor do parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | O ID de hardware do dispositivo |

Na carga útil de resposta, copie a propriedade de ligação do **dispositivoString.** Usa-o quando liga para o dispositivo Azure IoT SDK para enviar dados para gémeos digitais.

## <a name="device-to-cloud-message"></a>Mensagem dispositivo-para-nuvem

Pode personalizar o formato de mensagem e a carga útil do seu dispositivo de acordo com as necessidades da sua solução. Utilize qualquer contrato de dados que possa ser serializado numa matriz ou stream byte que seja suportado pela classe de Mensagem cliente do [dispositivo Azure IoT, Message(byte[byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). A mensagem pode ser um formato binário personalizado à sua escolha, desde que descodifique o contrato de dados numa função definida pelo utilizador correspondente. Só há um requisito para uma mensagem de dispositivo-para-nuvem. Mantenha um conjunto de propriedades para garantir que a sua mensagem seja encaminhada adequadamente para o motor de processamento.

### <a name="telemetry-properties"></a>Propriedades de telemetria

 O conteúdo da carga útil de uma **Mensagem** pode ser dados arbitrários até 256 KB em tamanho. São esperados alguns requisitos [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) para propriedades do tipo. A tabela mostra as propriedades necessárias e opcionais suportadas pelo sistema.

| Nome da propriedade | Valor | Necessário | Descrição |
|---|---|---|---|
| **DigitalTwins-Telemetria** | 1.0 | Sim | Um valor constante que identifica uma mensagem para o sistema. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Sim | Um identificador único do sensor que envia a **Mensagem.** Este valor deve corresponder à propriedade **hardwareId** de um objeto para o sistema processá-lo. Por exemplo, `00FF0643BE88-CO2`. |
| **CriaçãoTimeUtc** | `string` | Não | Uma cadeia de data formatada [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) que identifica o tempo de amostragem da carga útil. Por exemplo, `2018-09-20T07:35:00.8587882-07:00`. |
| **Correlalálada** | `string` | Não | Um UUID que é usado para rastrear eventos através do sistema. Por exemplo, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Envie a sua mensagem para Gémeos Digitais

Utilize a chamada Enviar [EventoAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [Enviar EventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) para enviar a sua mensagem para Gémeos Digitais.

## <a name="next-steps"></a>Passos seguintes

- Para conhecer o processamento de dados das Gémeas Digitais Azure e as capacidades de funções definidas pelo utilizador, leia o processamento de dados das [Gémeas Digitais Azure e as funções definidas pelo utilizador.](concepts-user-defined-functions.md)
