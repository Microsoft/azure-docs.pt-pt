---
title: Compreender os preços do Hub Azure IoT [ Microsoft Docs
description: Guia de desenvolvedores - informações sobre como a medição e os preços funcionam com o IoT Hub, incluindo exemplos trabalhados.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729150"
---
# <a name="azure-iot-hub-pricing-information"></a>Informação sobre preços do Hub Azure IoT

Os preços do [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) fornecem as informações gerais sobre diferentes SKUs e preços para ioT Hub. Este artigo contém detalhes adicionais sobre como as várias funcionalidades do IoT Hub são medidos como mensagens pelo IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informação de faturação | 
| --------- | ------------------- |
| Operações de registo de identidade <br/> (criar, recuperar, listar, atualizar, eliminar) | Não carregado. |
| Mensagens do dispositivo para a cloud | Mensagens enviadas com sucesso são carregadas em pedaços de 4 KB sobre entrada no IoT Hub. Por exemplo, uma mensagem de 6 KB é carregada 2 mensagens. |
| Mensagens cloud-to-device | As mensagens enviadas com sucesso são carregadas em pedaços de 4 KB, por exemplo, uma mensagem de 6 KB é carregada 2 mensagens. |
| Uploads de ficheiros | A transferência de ficheiros para o Armazenamento Azure não é mediante de IoT Hub. As mensagens de iniciação e conclusão da transferência de ficheiros são carregadas como mensagens medição em incrementos de 4 KB. Por exemplo, a transferência de um ficheiro de 10 MB é cobrada como duas mensagens para além do custo de Armazenamento Azure. |
| Métodos diretos | Os pedidos de métodos bem sucedidos são cobrados em pedaços de 4 KB, e as respostas são cobradas em pedaços de 4 KB como mensagens adicionais. Os pedidos para dispositivos desligados são carregados como mensagens em pedaços de 4 KB. Por exemplo, um método com um corpo de 4 KB que resulta numa resposta sem corpo do dispositivo é carregado como duas mensagens. Um método com um corpo de 6 KB que resulta numa resposta de 1 KB do dispositivo é carregado como duas mensagens para o pedido mais outra mensagem para a resposta. |
| Leituras gémeas de dispositivo e módulo | As leituras gémeas do dispositivo ou do módulo e da parte traseira da solução são carregadas como mensagens em pedaços de 512 bytes. Por exemplo, ler um gémeo de 6 KB é carregado como 12 mensagens. |
| Atualizações gémeas de dispositivos e módulos (tags e propriedades) | As atualizações gémeas do dispositivo ou do módulo e da parte traseira da solução são carregadas como mensagens em pedaços de 512 bytes. Por exemplo, ler um gémeo de 6 KB é carregado como 12 mensagens. |
| Consultas gémeas de dispositivo e módulo | As consultas são cobradas como mensagens dependendo do tamanho do resultado em pedaços de 512 bytes. |
| Operações de tarefas <br/> (criar, atualizar, listar, eliminar) | Não carregado. |
| Operações de emprego por dispositivo | As operações de emprego (tais como atualizações duplas e métodos) são cobradas normalmente. Por exemplo, é cobrado 1000 pedidos de métodos com pedidos de 1 KB e respostas de corpo vazio. |
| Mensagens de manter vivas | Ao utilizar protocolos AMQP ou MQTT, as mensagens trocadas para estabelecer a ligação e as mensagens trocadas na negociação não são cobradas. |

> [!NOTE]
> Todos os tamanhos são calculados tendo em conta o tamanho da carga útil em bytes (o enquadramento do protocolo é ignorado). Para mensagens, que têm propriedades e corpo, o tamanho é calculado de forma protocolar-agnóstica. Para mais informações, consulte o formato de [mensagem IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exemplo #1

Um dispositivo envia uma mensagem de 1 KB para cloud por minuto para o IoT Hub, que é depois lido pelo Azure Stream Analytics. A extremidade traseira da solução invoca um método (com uma carga útil de 512 bytes) no dispositivo a cada 10 minutos para desencadear uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consome:

* Uma mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para as mensagens dispositivo-a-nuvem.
* Dois pedidos mais resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos.

Este cálculo dá um total de 1728 mensagens por dia.

## <a name="example-2"></a>Exemplo #2

Um dispositivo envia uma mensagem de 100 KB para nuvem a cada hora. Também atualiza o seu dispositivo twin com cargas de 1 KB a cada quatro horas. A solução volta, uma vez por dia, lê o dispositivo 14-KB twin e atualiza-o com cargas de 512 bytes para alterar configurações.

O dispositivo consome:

* 25 (100 KB / 4 KB) mensagens * 24 horas para mensagens dispositivo-a-nuvem.
* Duas mensagens (1 KB / 0,5 KB) * seis vezes por dia para atualizações gémeas do dispositivo.

Este cálculo dá um total de 612 mensagens por dia.

A solução traseira consome 28 mensagens (14 KB / 0,5 KB) para ler o dispositivo twin, mais uma mensagem para atualizá-lo, num total de 29 mensagens.

No total, o dispositivo e a solução traseira consomem 641 mensagens por dia.
