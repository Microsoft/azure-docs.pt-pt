---
title: Configurando políticas de ordenação de eventos para Azure Stream Analytics
description: Este artigo descreve como definir as configurações de ordenação uniforme no Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461190"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurando políticas de ordenação de eventos para Azure Stream Analytics

Este artigo descreve como configurar e usar a chegada tardia e políticas de evento fora de ordem no Azure Stream Analytics. Essas políticas são aplicadas somente quando você usa a cláusula [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) em sua consulta.

## <a name="event-time-and-arrival-time"></a>Hora do evento e hora de chegada

Seu trabalho de Stream Analytics pode processar eventos com base na *hora do evento* ou na hora da *chegada*. O **tempo de evento/aplicativo** é o carimbo de data/hora presente na carga do evento (quando o evento foi gerado). O **tempo de chegada** é o carimbo de data/hora em que o evento foi recebido na fonte de entrada (hubs de eventos/Hub IOT/armazenamento de BLOBs). 

Por padrão, o Stream Analytics processa eventos por *hora de chegada*, mas você pode optar por processar eventos por hora do *evento* usando a cláusula [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) em sua consulta. A chegada tardia e as políticas fora de ordem serão aplicáveis somente se você processar eventos por hora do evento. Considere os requisitos de latência e correção para o seu cenário quando configurar estas definições. 

## <a name="what-is-late-arrival-policy"></a>O que é a política de chegada tardia?

Às vezes, os eventos chegam atrasados por vários motivos. Por exemplo, um evento que chega 40 segundos de atraso terá tempo de evento = 00:10:00 e hora de chegada = 00:10:40. Se você definir a política de chegada tardia para 15 segundos, qualquer evento que chega depois de 15 segundos será Descartado (não processado por Stream Analytics) ou terá seu tempo de evento ajustado. No exemplo acima, como o evento chegou 40 segundos atrasado (mais do que o conjunto de políticas), a hora do evento será ajustada para o máximo da política de chegada de atraso 00:10:25 (valor da política de chegada tardia). A política de chegada tardia padrão é de 5 segundos.

## <a name="what-is-out-of-order-policy"></a>O que é política fora de ordem? 

O evento pode chegar fora de ordem também. Após o ajuste da hora do evento com base na política de chegada tardia, você também pode optar por remover ou ajustar automaticamente os eventos que estão fora de ordem. Se você definir essa política como 8 segundos, todos os eventos que chegarem fora de ordem, mas dentro da janela de 8 segundos, serão reordenados por hora do evento. Os eventos que chegam posteriormente serão removidos ou ajustados para o valor máximo de política fora de ordem. A política de fora de ordem padrão é de 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajustar ou descartar eventos

Se os eventos chegarem atrasados ou fora de ordem com base nas políticas que você configurou, você poderá remover esses eventos (não processados por Stream Analytics) ou fazer com que o seu tempo de evento seja ajustado.

Vamos ver um exemplo dessas políticas em ação.
<br> **Política de chegada tardia:** 15 segundos
<br> **Política fora de ordem:** 8 segundos

| N º do evento | Hora do Evento | Hora de chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | O evento chegou ao nível de tolerância atrasado e externo. Portanto, a hora do evento é ajustada para a tolerância máxima de chegada tardia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | O evento chegou atrasado, mas dentro do nível de tolerância. Portanto, a hora do evento não é ajustada.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Evento recebido no prazo. Nenhum ajuste necessário.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | O evento chegou fora de ordem, mas dentro da tolerância de 8 segundos. Portanto, a hora do evento não é ajustada. Para fins de análise, esse evento será considerado como o número de evento anterior 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | O evento chegou fora de ordem e a tolerância externa de 8 segundos. Portanto, a hora do evento é ajustada para o máximo de tolerância de fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Essas configurações podem atrasar a saída do meu trabalho? 

Sim. Por padrão, a política fora de ordem é definida como zero (00 minutos e 00 segundos). Se você alterar o padrão, a primeira saída do trabalho será atrasada por esse valor (ou maior). 

Se uma das partições de suas entradas não receber eventos, você deverá esperar que a saída seja atrasada pelo valor da política de chegada tardia. Para saber o porquê, leia a seção de erro InputPartition abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vejo mensagens LateInputEvents no meu log de atividades

Essas mensagens são mostradas para informar que os eventos chegaram atrasados e foram descartados ou ajustados de acordo com sua configuração. Você poderá ignorar essas mensagens se tiver configurado a política de chegada tardia adequadamente. 

O exemplo dessa mensagem é: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vejo InputPartitionNotProgressing em meu log de atividades

A fonte de entrada (Hub de eventos/Hub IoT) provavelmente tem várias partições. Azure Stream Analytics produz a saída para o carimbo de data/hora T1 somente depois que todas as partições combinadas são pelo menos no tempo T1. Por exemplo, suponha que a consulta Leia a partir de uma partição do hub de eventos que tenha duas partições. Uma das partições, P1, tem eventos até o tempo T1. A outra partição, P2, tem eventos até o tempo T1 + x. A saída é produzida até o tempo T1. Mas se houver uma cláusula PARTITION by PartitionID explícita, ambas as partições progredim de forma independente. 

Quando várias partições do mesmo fluxo de entrada são combinadas, a tolerância de chegada tardia é a quantidade máxima de tempo que cada partição espera por novos dados. Se houver uma partição em seu hub de eventos ou se o Hub IoT não receber entradas, a linha do tempo dessa partição não progredirá até atingir o limite de tolerância de chegada tardia. Isso atrasa a saída pelo limite de tolerância de chegada tardia. Nesses casos, você pode ver a seguinte mensagem:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem para informar que pelo menos uma partição em sua entrada está vazia e atrasará a saída pelo limite de chegada tardia. Para superar isso, é recomendável:  
1. Verifique se todas as partições do hub de eventos/Hub IoT recebem entrada. 
2. Use a cláusula PARTITION by PartitionID em sua consulta. 

## <a name="next-steps"></a>Passos seguintes
* [Considerações de tratamento de tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
