---
title: Entrada de conectividade e telemetria de dispositivos duplos Digital do Azure | Documentos da Microsoft
description: Descrição geral de como colocar um dispositivo carregar duplos Digital do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 35d12d0114f9677905c85a9df94ecd074e5f8f75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926084"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Entrada de conectividade e telemetria de dispositivo

Os dados de telemetria enviados pelos dispositivos e sensores formam a estrutura principal de qualquer solução de IoT. Como representar estes recursos diferentes e geri-los no contexto de uma localização são chefe preocupações no desenvolvimento de aplicações de IoT. Duplos Digital do Azure simplifica o processo de desenvolvimento de soluções de IoT por unindo dispositivos e sensores com um gráfico de inteligência espaciais.

Para começar, crie um recurso do IoT Hub do Azure na raiz do gráfico espacial. O recurso do IoT Hub permite que todos os dispositivos abaixo o espaço de raiz para enviar mensagens. Depois de criar o IoT Hub, registe dispositivos com sensores dentro da instância de duplos Digital. Os dispositivos podem enviar dados para um serviço de duplos Digital através da [do Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Para obter um guia passo a passo sobre como colocar o carregar dispositivos, consulte a [Tutorial para implementar e configurar duplos Digital](tutorial-facilities-setup.md). Visão geral, as etapas são:

- Implementar uma instância de duplos Digital do [portal do Azure](https://portal.azure.com).
- Crie espaços no seu gráfico.
- Criar um recurso do IoT Hub e atribuí-lo a um espaço no seu gráfico.
- Crie dispositivos e sensores no seu gráfico e atribuí-las para os espaços criados nos passos anteriores.
- Crie na ferramenta para filtrar mensagens de telemetria com base nas condições de correspondência.
- Criar uma [função definida pelo utilizador](concepts-user-defined-functions.md)e atribuí-lo a um espaço no gráfico para o processamento personalizado de suas mensagens de telemetria.
- Atribua uma função para permitir que a função definida pelo utilizador acessar os dados de gráfico.
- Obter a cadeia de ligação de dispositivos do IoT Hub a partir das APIs de gestão de duplos Digital.
- Configure a cadeia de ligação do dispositivo no dispositivo com o SDK de dispositivo do IoT do Azure.

As secções seguintes, irá aprender a obter a cadeia de ligação de dispositivos do IoT Hub a partir da API de gestão de duplos Digital. Também irá aprender a utilizar o formato de mensagem de telemetria do IoT Hub para enviar telemetria com base em sensor. Duplos digitais requer que cada parte de telemetria que recebe a ser associado um sensor dentro do gráfico espacial. Este requisito assegura que os dados são processados e encaminhados dentro do contexto geográfico apropriado.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obter a cadeia de ligação de dispositivos do IoT Hub a partir da API de gestão

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Fazer uma chamada GET na API do dispositivo com um `includes=ConnectionString` parâmetro para obter a cadeia de ligação de dispositivos do IoT Hub. Filtre pelo dispositivo GUID ou o ID de hardware para encontrar o dispositivo especificado.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_GUID* | O ID de dispositivo |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valor do parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | O ID de hardware do dispositivo |

No payload de resposta, copiar do dispositivo **connectionString** propriedade. Utilize-ao chamar o Azure IoT device SDK para enviar dados para o Digital duplos.

## <a name="device-to-cloud-message"></a>Mensagem de dispositivo para a cloud

Pode personalizar o formato de mensagem do seu dispositivo e payload de acordo com as necessidades da sua solução. Utilize qualquer contrato de dados que pode ser serializado numa matriz de bytes ou fluxo que é suportado pelos [classe de mensagem de cliente de dispositivo de IoT do Azure, mensagem (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). A mensagem pode ser um formato binário personalizado à sua escolha, desde que decodificar o contrato de dados numa função definida pelo utilizador correspondente. Existe apenas um requisito para uma mensagem de dispositivo para a cloud. Tem de manter um conjunto de propriedades para se certificar de que a sua mensagem é devidamente encaminhada para o motor de processamento.

### <a name="telemetry-properties"></a>Propriedades de telemetria

 O conteúdo do payload de um **mensagem** podem ser dados arbitrários até 256 KB de tamanho. Existem alguns requisitos esperados para propriedades do [ `Message.Properties` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) tipo. A tabela mostra as propriedades necessárias e opcionais são suportadas pelo sistema.

| Nome da propriedade | Value | Necessário | Descrição |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Sim | Um valor constante que identifica uma mensagem ao sistema. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Sim | Um identificador exclusivo do sensor que envia o **mensagem**. Este valor tem de corresponder um objeto **HardwareId** propriedade para o sistema para processá-lo. Por exemplo, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Não | Uma [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatado de cadeia de data que identifica o tempo de amostragem da carga. Por exemplo, `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Não | Um UUID que utilizou para eventos de rastreio em todo o sistema de mensagens em fila. Por exemplo, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Enviar a mensagem para o Digital duplos

Utilizar o DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) chamada para enviar sua mensagem para o Digital duplos.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o processamento de dados de duplos Digital do Azure e as capacidades de funções definidas pelo utilizador, leia [processamento de dados de duplos Digital do Azure e funções definidas pelo utilizador](concepts-user-defined-functions.md).
