---
title: Conectividade do dispositivo e entrada de telemetria-Azure digital gêmeos | Microsoft Docs
description: Visão geral de como colocar um dispositivo integrado ao Azure digital gêmeos
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 723fe14db9089e1127f39eae3ed7b10bbddf70bf
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889716"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Entrada de conectividade e telemetria de dispositivo

Os dados de telemetria enviados por dispositivos e sensores formam o backbone de qualquer solução de IoT. Como representar esses recursos diferentes e gerenciá-los dentro do contexto de um local são as principais preocupações no desenvolvimento de aplicativos de IoT. O Azure digital gêmeos simplifica o processo de desenvolvimento de soluções de IoT por meio da unidade de dispositivos e sensores com um grafo de inteligência espacial.

Para começar, crie um recurso do Hub IoT do Azure na raiz do grafo espacial. O recurso Hub IoT permite que todos os dispositivos sob o espaço raiz enviem mensagens. Depois que o Hub IoT for criado, registre os dispositivos com sensores na instância digital gêmeos. Os dispositivos podem enviar dados para um serviço gêmeos digital por meio do [SDK do dispositivo IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Para obter um guia passo a passo sobre como colocar dispositivos integrados, consulte o [tutorial para implantar e configurar o digital gêmeos](tutorial-facilities-setup.md). Em um relance, as etapas são:

- Implante uma instância de gêmeos digital do [portal do Azure](https://portal.azure.com).
- Crie espaços em seu grafo.
- Crie um recurso do Hub IoT e atribua-o a um espaço em seu grafo.
- Crie dispositivos e sensores em seu grafo e atribua-os aos espaços criados nas etapas anteriores.
- Crie um correspondente para filtrar mensagens de telemetria com base em condições.
- Crie uma [função definida pelo usuário](concepts-user-defined-functions.md)e atribua-a a um espaço no grafo para o processamento personalizado de suas mensagens de telemetria.
- Atribua uma função para permitir que a função definida pelo usuário acesse os dados do grafo.
- Obtenha a cadeia de conexão do dispositivo do Hub IoT das APIs de gerenciamento de gêmeos digital.
- Configure a cadeia de conexão do dispositivo no dispositivo com o SDK do dispositivo IoT do Azure.

Nas seções a seguir, você aprenderá a obter a cadeia de conexão do dispositivo do Hub IoT da API de gerenciamento de gêmeos digital. Você também aprenderá a usar o formato de mensagem de telemetria do Hub IoT para enviar telemetria baseada em sensor. O digital gêmeos requer que cada parte da telemetria que ele recebe esteja associada a um sensor dentro do grafo espacial. Esse requisito verifica se os dados são processados e roteados dentro do contexto espacial apropriado.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obter a cadeia de conexão do dispositivo do Hub IoT da API de gerenciamento

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Faça uma chamada GET na API do dispositivo com um parâmetro `includes=ConnectionString` para obter a cadeia de conexão do dispositivo do Hub IoT. Filtre pelo GUID do dispositivo ou pela ID de hardware para localizar o dispositivo fornecido.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_GUID* | A ID do dispositivo |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valor do parâmetro | Substituir |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | A ID de hardware do dispositivo |

Na carga de resposta, copie a propriedade **ConnectionString** do dispositivo. Você o usa quando chama o SDK do dispositivo IoT do Azure para enviar dados para o digital gêmeos.

## <a name="device-to-cloud-message"></a>Mensagem do dispositivo para a nuvem

Você pode personalizar o formato e a carga de mensagens do dispositivo para atender às necessidades da sua solução. Use qualquer contrato de dados que possa ser serializado em uma matriz de bytes ou fluxo com suporte da [classe de mensagem do cliente do dispositivo IOT do Azure, mensagem (Byte [] ByteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). A mensagem pode ser um formato binário personalizado de sua escolha, desde que você decodifique o contrato de dados em uma função definida pelo usuário correspondente. Há apenas um requisito para uma mensagem do dispositivo para a nuvem. Mantenha um conjunto de propriedades para certificar-se de que sua mensagem seja roteada adequadamente para o mecanismo de processamento.

### <a name="telemetry-properties"></a>Propriedades de telemetria

 O conteúdo da carga de uma **mensagem** pode ser dados arbitrários de até 256 KB de tamanho. Há alguns requisitos esperados para as propriedades do tipo de [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) . A tabela mostra as propriedades obrigatórias e opcionais com suporte no sistema.

| Nome da propriedade | Valor | Necessário | Descrição |
|---|---|---|---|
| **DigitalTwins-telemetria** | 1.0 | Sim | Um valor constante que identifica uma mensagem para o sistema. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Sim | Um identificador exclusivo do sensor que envia a **mensagem**. Esse valor deve corresponder à propriedade **HardwareID** de um objeto para o sistema processá-lo. Por exemplo, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Não | Uma cadeia de caracteres de data formatada [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que identifica o tempo de amostragem da carga. Por exemplo, `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Não | Um UUID que é usado para rastrear eventos no sistema. Por exemplo, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Enviar sua mensagem para o digital gêmeos

Use a chamada DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) para enviar sua mensagem para o gêmeos digital.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o processamento de dados do Azure digital gêmeos e recursos de funções definidas pelo usuário, leia [processamento de dados do gêmeos e funções definidas pelo usuário do Azure digital](concepts-user-defined-functions.md).
