---
title: Opções de dispositivo-para-cloud do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – documentação de orientação sobre quando utilizar mensagens de dispositivo para a cloud, as propriedades comunicadas ou carregamento de ficheiros para comunicações de cloud-para-dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57007986"
---
# <a name="device-to-cloud-communications-guidance"></a>Orientações de comunicações de dispositivo para a cloud

Ao enviar informações a partir da aplicação de dispositivo para a solução de back-end, o IoT Hub expõe três opções:

* [Mensagens do dispositivo para cloud](iot-hub-devguide-messages-d2c.md) de telemetria de série de tempo e alertas.

* [Dispositivo duplo do propriedades comunicadas](iot-hub-devguide-device-twins.md) por comunicarem as informações de estado do dispositivo, como recursos disponíveis, condições ou o estado dos fluxos de trabalho de longa execução. Por exemplo, configuração e atualizações de software.

* [Carregamentos de ficheiros](iot-hub-devguide-file-upload.md) para suporte de dados de ficheiros e lotes de telemetria de grandes carregados por dispositivos ligados intermitentemente ou comprimido para poupar largura de banda.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Aqui está uma comparação detalhada das várias opções de comunicação do dispositivo para a cloud.

|  | Mensagens do dispositivo para a cloud | Propriedades comunicadas do dispositivo duplo | Carrega o ficheiro |
| ---- | ------- | ---------- | ---- |
| Cenário | Série de tempo de telemetria e alertas. Por exemplo, os lotes de dados do sensor de 256 KB enviado a cada 5 minutos. | Funções disponíveis e condições. Por exemplo, o dispositivo conectividade modo atual, como rede móvel ou Wi-Fi. A sincronizar os fluxos de trabalho de longa execução, como atualizações de software e configuração. | Ficheiros de multimédia. Lotes grandes de telemetria (normalmente compactado). |
| Armazenamento e recuperação | Temporariamente armazenada pelo IoT Hub, até 7 dias. Apenas leitura sequencial. | Armazenados pelo IoT Hub no dispositivo duplo. Recuperável utilizando o [linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md). | Armazenados na conta de armazenamento do Azure fornecidos pelo usuário. |
| Tamanho | Até mensagens de 256 KB. | Tamanho de propriedades comunicadas máximo é de 8 KB. | Tamanho máximo suportado pelo armazenamento de Blobs do Azure. |
| Frequência | Elevada. Para obter mais informações, consulte [IoT Hub limita](iot-hub-devguide-quotas-throttling.md). | Média. Para obter mais informações, consulte [IoT Hub limita](iot-hub-devguide-quotas-throttling.md). | Baixo. Para obter mais informações, consulte [IoT Hub limita](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponível em todos os protocolos. | Disponível com MQTT ou AMQP. | Estão disponíveis ao utilizar qualquer protocolo, mas requer HTTPS no dispositivo. |

Uma aplicação poderá ter de enviar informações tanto como um alerta ou uma série de tempo de telemetria e disponibilizá-la no dispositivo duplo. Neste cenário, pode escolher uma das seguintes opções:

* A aplicação de dispositivo envia uma mensagem de dispositivo para a cloud e relatórios uma alteração de propriedade.
* A solução de back-end pode armazenar as informações de etiquetas do dispositivo duplo, ao receber a mensagem.

Uma vez que as mensagens do dispositivo para cloud ativar um débito mais elevado que as atualizações do dispositivo duplo de, por vezes, é desejável para evitar a atualizar o dispositivo duplo para todas as mensagens do dispositivo para a cloud.