---
title: Compreenda os preços do Azure IoT Hub | Microsoft Docs
description: Developer guide - informações sobre como a medição e os preços funcionam com o IoT Hub, incluindo exemplos trabalhados.
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
ms.openlocfilehash: 5b67f505a71d2af7950422a86ab45ef8aebb3bf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056441"
---
# <a name="azure-iot-hub-pricing-information"></a>Informações sobre preços do Azure IoT Hub

[Os preços do Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) fornecem as informações gerais sobre diferentes SKUs e preços para o IoT Hub. Este artigo contém detalhes adicionais sobre como as várias funcionalidades do IoT Hub são medidos como mensagens por IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informação de faturação | 
| --------- | ------------------- |
| Operações de registo de identidade <br/> (criar, recuperar, listar, atualizar, excluir) | Não cobrado. |
| Mensagens do dispositivo para a cloud | As mensagens enviadas com sucesso são carregadas em pedaços de 4-KB em entrada no IoT Hub. Por exemplo, uma mensagem de 6-KB é carregada 2 mensagens. |
| Mensagens nuvem-para-dispositivo | As mensagens enviadas com sucesso são carregadas em pedaços de 4-KB, por exemplo, uma mensagem de 6-KB é carregada 2 mensagens. |
| Uploads de ficheiros | A transferência de ficheiros para o Azure Storage não é medido pelo IoT Hub. As mensagens de iniciação e conclusão de transferência de ficheiros são carregadas como medidos por mensagem em incrementos de 4-KB. Por exemplo, a transferência de um ficheiro de 10 MB é cobrada como duas mensagens para além do custo de Armazenamento Azure. |
| Métodos diretos | Os pedidos de métodos bem sucedidos são cobrados em pedaços de 4-KB, e as respostas são cobradas em pedaços de 4-KB como mensagens adicionais. Os pedidos para dispositivos desligados são carregados como mensagens em pedaços 4-KB. Por exemplo, um método com um corpo de 4-KB que resulta numa resposta sem corpo do dispositivo é carregado como duas mensagens. Um método com um corpo de 6-KB que resulta numa resposta de 1-KB do dispositivo é carregado como duas mensagens para o pedido mais outra mensagem para a resposta. |
| Leituras gémeas do dispositivo e módulo | As leituras de twin do dispositivo ou módulo e da parte traseira da solução são carregadas como mensagens em pedaços de 4 KB. Por exemplo, ler um gémeo de 8 KB é cobrado como 2 mensagens. |
| Atualizações gémeas do dispositivo e do módulo (tags e propriedades) | As atualizações duplas do dispositivo ou módulo e da parte traseira da solução são carregadas como mensagens em pedaços de 4 KB. Por exemplo, ler um gémeo de 12 KB é cobrado como 3 mensagens. |
| Consultas de módulo e módulo | As consultas são carregadas como mensagens dependendo do tamanho do resultado em pedaços de 4-KB. |
| Operações de tarefas <br/> (criar, atualizar, listar, eliminar) | Não cobrado. |
| Operações de emprego por dispositivo | As operações de emprego (como atualizações duplas e métodos) são cobradas normalmente. Por exemplo, um trabalho que resulte em 1000 chamadas de método com pedidos de 1-KB e respostas de corpo vazio é cobrado 1000 mensagens. |
| Mensagens vivas | Ao utilizar protocolos AMQP ou MQTT, as mensagens trocadas para estabelecer a ligação e as mensagens trocadas na negociação não são cobradas. |

> [!NOTE]
> Todos os tamanhos são calculados tendo em conta o tamanho da carga útil nos bytes (o enquadramento do protocolo é ignorado). Para mensagens, que têm propriedades e corpo, o tamanho é calculado de forma protocolar-agnóstica. Para mais informações, consulte [o formato de mensagem IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exemplo #1

Um dispositivo envia uma mensagem de 1-KB de dispositivo para nuvem por minuto para o IoT Hub, que é depois lido pelo Azure Stream Analytics. A solução traseira invoca um método (com uma carga útil de 512 bytes) no dispositivo a cada 10 minutos para desencadear uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consome:

* Uma mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para as mensagens dispositivo-a-nuvem.
* Dois pedidos mais resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos.

Este cálculo dá um total de 1728 mensagens por dia.

## <a name="example-2"></a>Exemplo #2

Um dispositivo envia uma mensagem de 100 KB para nuvem a cada hora. Também atualiza o seu dispositivo twin com cargas de 1-KB a cada quatro horas. A solução back end, uma vez por dia, lê o duplo do dispositivo de 14 KB e atualiza-o com cargas de 512 bytes para alterar as configurações.

O dispositivo consome:

* 25 (100 KB / 4 KB) mensagens * 24 horas para mensagens dispositivo-a-nuvem.
* Duas mensagens (1 KB / 0,5 KB) * seis vezes por dia para atualizações gémeas do dispositivo.

Este cálculo dá um total de 612 mensagens por dia.

A solução back end consome 28 mensagens (14 KB / 0,5 KB) para ler o dispositivo twin, mais uma mensagem para atualizá-lo, num total de 29 mensagens.

No total, o dispositivo e a solução traseira consomem 641 mensagens por dia.
