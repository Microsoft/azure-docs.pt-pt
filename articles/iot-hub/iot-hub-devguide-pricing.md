---
title: Compreender os preços do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – informações sobre como medição e funcionam os preços com o IoT Hub, incluindo exemplos funcionavam.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 54336de291d01f3741c288756095e40a43800a7f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009992"
---
# <a name="azure-iot-hub-pricing-information"></a>Informações de preços do Hub de IoT do Azure

[Preços do IoT Hub do Azure](https://azure.microsoft.com/pricing/details/iot-hub) fornece as informações gerais sobre diferentes SKUs e preços para o IoT Hub. Este artigo contém detalhes adicionais sobre como as diversas funcionalidades do IoT Hub são medidas como mensagens pelo IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Cobrança por operação

| Operação | Informações de faturação | 
| --------- | ------------------- |
| Operações de registo de identidade <br/> (criar, obter, listar, atualizar e eliminar) | Não será cobrado. |
| Mensagens do dispositivo para a cloud | Mensagens enviadas com êxito são cobradas em blocos de 4 KB na entrada para o IoT Hub. Por exemplo, uma mensagem de 6 KB é cobrada 2 mensagens. |
| Mensagens da cloud para dispositivo | Mensagens enviadas com êxito são cobradas em blocos de 4 KB, por exemplo uma mensagem de 6 KB é cobrada 2 mensagens. |
| Carrega o ficheiro | Transferência de ficheiros para o armazenamento do Azure não tem tráfego limitada pelo IoT Hub. Mensagens de início e conclusão de transferência do ficheiro são cobradas conforme messaged medido em incrementos de 4 KB. Por exemplo, transferir um ficheiro de 10 MB é cobrada duas mensagens além do custo de armazenamento do Azure. |
| Métodos diretos | Pedidos com êxito do método são cobrados em blocos de 4 KB, as respostas com corpos de não vazios são cobradas em blocos de 4 KB como mensagens adicionais. Pedidos para os dispositivos desligados são cobrados conforme as mensagens em blocos de 4 KB. Por exemplo, um método com um corpo de 6 KB resulta numa resposta com nenhum corpo de dispositivo, é cobrado como duas mensagens. Um método com um corpo de 6 KB resulta numa resposta de 1 KB do dispositivo é cobrado como duas mensagens para o pedido e ainda outra mensagem para a resposta. |
| Leituras de dispositivo e o módulo duplo | Duplo lê a partir do dispositivo ou do módulo e para a solução de back-end é cobrada conforme as mensagens em blocos de 512 bytes. Por exemplo, a leitura de um gémeo 6 KB é cobrado como 12 mensagens. |
| Atualizações de dispositivo e o módulo duplo (etiquetas e propriedades) | Atualizações de Twin do dispositivo ou módulo e para o back-end de solução são cobradas conforme as mensagens em blocos de 512 bytes. Por exemplo, a leitura de um gémeo 6 KB é cobrado como 12 mensagens. |
| Consultas de gémeos de dispositivo e módulo | Consultas são cobradas como mensagens, dependendo do tamanho do resultado em blocos de 512 bytes. |
| Operações de tarefas <br/> (criar, atualizar, listar, eliminar) | Não será cobrado. |
| Operações das tarefas por dispositivo | Operações de tarefas (como atualizações de gémeos e métodos) são cobradas como normal. Por exemplo, uma tarefa, resultando em chamadas de método de 1000 com solicitações de 1 KB e respostas de corpo vazio é cobrada 1000 mensagens. |
| Mensagens Keep-alive | Ao utilizar protocolos AMQP ou MQTT, mensagens trocadas para estabelecer a ligação e as mensagens trocadas na negociação não são cobradas. |

> [!NOTE]
> Todas as dimensões são calculadas considerando o tamanho de payload em bytes (delimitação de quadros do protocolo é ignorada). Para mensagens, que tem propriedades e o corpo, o tamanho é calculado de forma independente de protocolo. Para obter mais informações, consulte [formato de mensagem do IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Exemplo #1

Um dispositivo envia uma mensagem de dispositivo para a cloud de 1 KB por minuto para o IoT Hub, que, em seguida, é lido pelo Azure Stream Analytics. A solução de back-end invoca um método (com o payload de 512 bytes) no dispositivo a cada 10 minutos para acionar uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consuma:

* Uma mensagem * 60 minutos * 24 horas = 1440 de mensagens por dia para as mensagens do dispositivo para a cloud.
* Dois do pedido e resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos.

Este cálculo dá um total de mensagens de 1728 por dia.

## <a name="example-2"></a>Exemplo #2

Um dispositivo envia uma mensagem de dispositivo para a cloud de 100 KB a cada hora. Ele também atualiza o dispositivo duplo com payloads de 1 KB a cada quatro horas. A solução de back-end, uma vez por dia, lê o twin do dispositivo de 14 KB e atualiza-o com payloads de 512 bytes para alterar as configurações.

O dispositivo consuma:

* 25 mensagens de (100 KB/4 KB) * 24 horas para mensagens de dispositivo para a cloud.
* Duas mensagens (1 KB/0,5 KB) * seis vezes por dia para atualizações de dispositivo duplo.

Este cálculo dá um total de 612 mensagens por dia.

A solução de back-end consome 28 mensagens (14 KB/0,5 KB) para ler o dispositivo duplo, além de uma mensagem para atualizá-lo, para um total de 29 mensagens.

No total, o dispositivo e o back-end de solução consumam 641 mensagens por dia.
