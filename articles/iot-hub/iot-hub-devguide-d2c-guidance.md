---
title: Opções de dispositivo-para-nuvem Azure IoT Hub [ Azure IoT Hub] Microsoft Docs
description: Guia de desenvolvedores - orientação sobre quando usar mensagens dispositivo-nuvem, propriedades reportadas ou upload de ficheiros para comunicações cloud-to-device.
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
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733218"
---
# <a name="device-to-cloud-communications-guidance"></a>Orientação de comunicações dispositivo-nuvem

Ao enviar informações da aplicação do dispositivo para a extremidade traseira da solução, o IoT Hub expõe três opções:

* [Mensagens dispositivo-a-nuvem](iot-hub-devguide-messages-d2c.md) para telemetria e alertas da série de tempo.

* [As propriedades reportadas pela Device Twin](iot-hub-devguide-device-twins.md) para reportar informações estatais do dispositivo, tais como capacidades disponíveis, condições ou o estado dos fluxos de trabalho de longo prazo. Por exemplo, configurações e atualizações de software.

* [Uploads de ficheiros](iot-hub-devguide-file-upload.md) de ficheiros de ficheiros e grandes lotes de telemetria carregados por dispositivos intermitentemente ligados ou comprimidos para salvar a largura de banda.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Aqui está uma comparação detalhada das várias opções de comunicação dispositivo-nuvem.

|  | Mensagens do dispositivo para a cloud | Propriedades reportadas do dispositivo twin | Uploads de ficheiros |
| ---- | ------- | ---------- | ---- |
| Cenário | Telemetria série seleção e alertas. Por exemplo, os lotes de dados de sensores 256-KB enviados a cada 5 minutos. | Capacidades e condições disponíveis. Por exemplo, o atual modo de conectividade do dispositivo, como o telemóvel ou o WiFi. Sincronizar fluxos de trabalho de longo prazo, tais como configurações e atualizações de software. | Ficheiros da imprensa. Lotes de telemetria grandes (tipicamente comprimidos). |
| Armazenamento e recuperação | Temporariamente armazenado pelo IoT Hub, até 7 dias. Só leitura sequencial. | Armazenado por IoT Hub no dispositivo twin. Recuperável utilizando a [linguagem de consulta IoT Hub](iot-hub-devguide-query-language.md). | Armazenado na conta de Armazenamento Azure fornecida pelo utilizador. |
| Tamanho | Até 256-KB mensagens. | O tamanho máximo das propriedades reportadas é de 32 KB. | Tamanho máximo do ficheiro suportado pelo Armazenamento De Blob Azure. |
| Frequência | Elevada. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Média. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Baixo. Para mais informações, consulte [os limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponível em todos os protocolos. | Disponível através de MQTT ou AMQP. | Disponível quando utilizar qualquer protocolo, mas requer HTTPS no dispositivo. |

Uma aplicação pode ter de enviar informações tanto como uma série de horário sinuoso ou alerta e disponibilizá-la no dispositivo twin. Neste cenário, pode escolher uma das seguintes opções:

* A aplicação do dispositivo envia uma mensagem de dispositivo para nuvem e reporta uma mudança de propriedade.
* A extremidade traseira da solução pode armazenar a informação nas etiquetas do dispositivo twin quando recebe a mensagem.

Uma vez que as mensagens dispositivo-nuvem permitem uma entrada muito mais elevada do que as atualizações gémeas do dispositivo, por vezes é desejável evitar atualizar o dispositivo twin para cada mensagem dispositivo-cloud.
