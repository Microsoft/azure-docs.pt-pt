---
title: Configurar políticas para o Azure Stream Analytics de ordenação de eventos
description: Este artigo descreve como configurar a ordenação até mesmo as definições do Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190565"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurar políticas para o Azure Stream Analytics de ordenação de eventos

Este artigo descreve como configurar e utilizar políticas de fora de ordem de eventos e de chegada tardia no Azure Stream Analytics. Estas políticas são aplicadas apenas quando utiliza a [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) cláusula na sua consulta.

## <a name="event-time-and-arrival-time"></a>Hora do evento e a hora de chegada

A tarefa de Stream Analytics consegue processar eventos com base numa *hora do evento* ou *hora da chegada*. **Hora do evento/aplicação** é o carimbo de hora presente no payload do evento (quando o evento foi gerado). **Hora da chegada** é o carimbo de hora, quando o evento foi recebido na origem de entrada (armazenamento de Event Hubs/IoT Hub/BLOBs). 

Por predefinição, o Stream Analytics processa eventos por *hora da chegada*, mas pode escolher processar eventos por *hora do evento* utilizando [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) cláusula na sua consulta. As políticas de chegada e fora de ordem tardia são aplicáveis apenas se a processar eventos por hora do evento. Considere os requisitos de latência e correção para o seu cenário quando configurar estas definições. 

## <a name="what-is-late-arrival-policy"></a>Qual é a política de chegada tardia?

Por vezes, eventos se atrasam devido a vários motivos. Por exemplo, um evento que chega 40 segundos mais tarde será ter a hora do evento = tempo 00:00 10: e chegada = 10:00:40. Se definir a política de chegada tardia 15 segundos, de qualquer evento que chegam mais tarde do que 15 segundos ou é largado (não processados pelo Stream Analytics) ou tiver ajustado de seu tempo de eventos. No exemplo acima, como o evento chegou 40 segundos mais tarde (mais de conjunto de políticas), a hora do evento será ajustada para o número máximo de chegada política 00:10:25 (hora de chegada - o valor de política de chegada final). Política de chegada tardia predefinida é de 5 segundos.

## <a name="what-is-out-of-order-policy"></a>Qual é a política de fora de ordem? 

Evento pode chegar fora de ordem também. Após a hora do evento é ajustada com base na política de chegada tardia, também pode optar por automaticamente, remova ou ajustar os eventos que estão fora de ordem. Se definir esta política para 8 segundos, todos os eventos que chegam fora de ordem, mas dentro da janela de 8 segundos são reordenados por hora do evento. Eventos que chegam mais tarde irão ser removidos ou ajustados para o valor máximo de fora de ordem de política. Política de fora de ordem predefinida é 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajustar ou remover eventos

Se os eventos chegarem atrasado ou fora de ordem com base em políticas configuradas, pode remover esses eventos (não processados pelo Stream Analytics) ou tempo eventos ajustado.

Vamos ver um exemplo dessas diretivas em ação.
<br> **Política de chegada tardia:** 15 segundos
<br> **Política de fora de ordem:** 8 segundos

| Evento n º | Tempo do evento | Hora da chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Evento disponível a nível de tolerância tardia e externos. Então, hora do evento seja ajustada para tolerância máxima de chegada tardia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Evento chegou tarde, mas dentro de nível de tolerância. Portanto, a hora do evento não obter ajustada.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Evento disponível no tempo. Não ajuste necessário.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Evento disponível fora de ordem, mas pertencer a tolerância de 8 segundos. Por isso, a hora do evento não obter ajustada. Para fins de análise, este evento será considerado como antes do número de evento 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evento disponível de tolerância de fora de ordem e fora dos 8 segundos. Por isso, a hora do evento é ajustada para o máximo de tolerância de fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Estas definições podem atrasar a saída do meu trabalho? 

Sim. Por predefinição, a política de fora de ordem estiver definida para zero (00 minutos e 00 segundos). Se alterar a predefinição, a saída da tarefa de primeiro é atrasado por este valor (ou superior). 

Se uma das partições de suas entradas não receber eventos, deve esperar a saída para ser atrasado o valor da política de chegada tardia. Para saber por que, leia a secção de erro InputPartition abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Posso ver mensagens de LateInputEvents no meu registo de atividades

Estas mensagens são apresentadas para o informar que eventos tem chegou tarde e são ignorados ou que ajusta de acordo com a configuração. Pode ignorar estas mensagens se tiver configurado a política de chegada tardia adequadamente. 

Exemplo desta mensagem é: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vejo InputPartitionNotProgressing no meu registo de atividades

A origem de entrada (Hub de eventos de Hub/IoT) provavelmente tem várias partições. O Azure Stream Analytics produz a saída para t1 de carimbo de data / hora apenas depois de todas as partições são combinadas, pelo menos, estão na hora t1. Por exemplo, suponha que a consulta lê a partir de uma partição do hub de eventos que tem duas partições. Uma das partições, P1, tem eventos até tempo t1. A outra partição, P2, tem eventos até tempo t1 + x. Em seguida, ser produzido um resultado até tempo t1. Mas, se existir uma partição explícita por PartitionId cláusula, ambas as partições progride de forma independente. 

Quando são combinadas várias partições a partir do mesmo fluxo de entrada, a tolerância de chegada tardia é a quantidade máxima de tempo que cada partição aguarda novos dados. Se ocorrer uma partição no seu Hub de eventos, ou se o IoT Hub não receber entradas, a linha cronológica para essa partição não progride até atingir o limiar de tolerância de chegada tardia. Isso atrasa a saída pelo limiar de tolerância de chegada tardia. Nesses casos, poderá ver a mensagem seguinte:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem a informar de que pelo menos uma partição na sua entrada está vazia e atrasará o resultado pelo limiar de chegada tardia. Para resolver isso, é recomendado que qualquer um dos: 1. Certifique-se de que todas as partições do Hub de Hub/IoT de eventos recebem entrada. 2. Utilizar partição por PartitionID cláusula na sua consulta. 

## <a name="next-steps"></a>Passos Seguintes
* [Considerações de manipulação de tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
