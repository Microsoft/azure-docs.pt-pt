---
title: Opções de dispositivo-nuvem Azure IoT Hub | Microsoft Docs
description: Guia do programador - orientação sobre quando usar mensagens de dispositivo para nuvem, propriedades relatadas ou upload de ficheiros para comunicações nuvem-dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd4f4ad7f84ebc1f5e254843e7afa4aa0f3f224f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87319138"
---
# <a name="device-to-cloud-communications-guidance"></a>Orientação de comunicações dispositivo-nuvem

Ao enviar informações da aplicação do dispositivo para a solução back end, o IoT Hub expõe três opções:

* [Mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md) para telemetria e alertas da série de tempo.

* [As propriedades reportadas pela Gémea do Dispositivo](iot-hub-devguide-device-twins.md) para reportar informações do estado do dispositivo, tais como capacidades, condições disponíveis ou o estado de fluxos de trabalho de longa duração. Por exemplo, configurações e atualizações de software.

* [Os uploads de ficheiros](iot-hub-devguide-file-upload.md) para ficheiros de mídia e grandes lotes de telemetria carregados por dispositivos intermitentemente ligados ou comprimidos para salvar a largura de banda.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Aqui está uma comparação detalhada das várias opções de comunicação dispositivo-a-nuvem.

| Fator | Mensagens do dispositivo para a cloud | Propriedades reportadas do Device Twin | Uploads de ficheiros |
| ---- | ------- | ---------- | ---- |
| Scenario | Séries e alertas de telemetria. Por exemplo, os lotes de dados de sensores 256-KB enviados a cada 5 minutos. | Capacidades e condições disponíveis. Por exemplo, o modo de conectividade do dispositivo atual, como celular ou Wi-Fi. Sincronizar fluxos de trabalho de longa duração, tais como configurações e atualizações de software. | Ficheiros de mídia. Grandes lotes de telemetria (tipicamente comprimidos). |
| Armazenamento e recuperação | Temporariamente armazenada pelo IoT Hub, até 7 dias. Só leitura sequencial. | Armazenado por IoT Hub no dispositivo twin. Recuperável utilizando a [linguagem de consulta IoT Hub](iot-hub-devguide-query-language.md). | Armazenada na conta Azure Storage fornecida pelo utilizador. |
| Tamanho | Até 256-KB mensagens. | O tamanho máximo das propriedades reportadas é de 32 KB. | Tamanho máximo do ficheiro suportado pelo Azure Blob Storage. |
| Frequência | Elevada. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) | Média. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) | Baixo. Para mais informações, consulte [os limites do IoT Hub.](iot-hub-devguide-quotas-throttling.md) |
| Protocolo | Disponível em todos os protocolos. | Disponível usando MQTT ou AMQP. | Disponível ao utilizar qualquer protocolo, mas requer HTTPS no dispositivo. |

Uma aplicação pode ter de enviar informações tanto como série de tempo de telemetria como alerta e torná-la disponível no dispositivo twin. Neste cenário, pode escolher uma das seguintes opções:

* A aplicação do dispositivo envia uma mensagem de dispositivo para nuvem e relata uma mudança de propriedade.
* A parte traseira da solução pode armazenar as informações nas etiquetas do twin do dispositivo quando recebe a mensagem.

Uma vez que as mensagens dispositivo-nuvem permitem uma produção muito maior do que as atualizações gémeas do dispositivo, por vezes é desejável evitar a atualização do dispositivo twin para cada mensagem dispositivo-nuvem.
