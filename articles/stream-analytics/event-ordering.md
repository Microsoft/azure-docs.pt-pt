---
title: Configurar políticas de ordenação de eventos para O Analytics do Stream
description: Este artigo descreve como configurar até mesmo encomendar definições no Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75461190"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurar políticas de ordenação de eventos para O Analytics do Stream

Este artigo descreve como configurar e usar políticas de eventos de chegada tardia e fora de ordem no Azure Stream Analytics. Estas políticas só são aplicadas quando utiliza a cláusula [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) na sua consulta.

## <a name="event-time-and-arrival-time"></a>Hora do evento e hora de chegada

O seu trabalho no Stream Analytics pode processar eventos com base na hora do *evento* ou na *hora de chegada*. **O tempo de evento/aplicação** é o carimbo de tempo presente na carga útil do evento (quando o evento foi gerado). **A hora** de chegada é a marca de tempo quando o evento foi recebido na fonte de entrada (Event Hubs/IoT Hub/Blob storage). 

Por padrão, o Stream Analytics processa eventos à hora de *chegada,* mas pode optar por processar eventos por hora de *evento,* utilizando a cláusula [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) na sua consulta. As políticas de chegada tardia e fora de encomenda só são aplicáveis se processar eventos por hora de evento. Considere os requisitos de latência e correção para o seu cenário quando configurar estas definições. 

## <a name="what-is-late-arrival-policy"></a>O que é a política de chegada tardia?

Às vezes os acontecimentos chegam atrasados por várias razões. Por exemplo, um evento que chega 40 segundos atrasado terá tempo de evento = 00:10:00 e hora de chegada = 00:10:40. Se definir a política de chegada tardia para 15 segundos, qualquer evento que chegue mais tarde de 15 segundos será abandonado (não processado pelo Stream Analytics) ou terá o seu tempo de evento ajustado. No exemplo acima, à medida que o evento chegou 40 segundos atrasado (mais do que o conjunto de políticas), o seu tempo de evento será ajustado ao máximo da política de chegada tardia 00:10:25 (hora de chegada - valor da política de chegada tardia). A política de chegada tardia padrão é de 5 segundos.

## <a name="what-is-out-of-order-policy"></a>O que é política fora de ordem? 

O evento também pode chegar fora de ordem. Após o tempo de evento ser ajustado com base na política de chegada tardia, também pode optar por deixar cair ou ajustar automaticamente eventos que estão fora de serviço. Se definir esta política para 8 segundos, quaisquer eventos que cheguem fora de ordem, mas dentro da janela de 8 segundos são reordenados pela hora do evento. Os eventos que chegarem mais tarde serão abandonados ou ajustados ao valor máximo da política fora de ordem. A política de incumprimento fora de ordem é de 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajuste ou Deixe cair eventos

Se os eventos chegarem atrasados ou fora de ordem com base nas políticas configuradas, pode abandonar tais eventos (não processados pelo Stream Analytics) ou ter o seu tempo de evento ajustado.

Vejamos um exemplo destas políticas em ação.
<br> **Política de chegada tardia:** 15 segundos
<br> **Política fora de ordem:** 8 segundos

| Evento Nº. | Hora do Evento | Hora de Chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | O evento chegou atrasado e fora do nível de tolerância. Assim, o tempo do evento é ajustado à tolerância máxima de chegada tardia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | O evento chegou tarde, mas dentro do nível de tolerância. Assim, a hora do evento não é ajustada.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | O evento chegou a tempo. Não é necessário ajuste.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | O evento chegou fora de ordem, mas dentro da tolerância de 8 segundos. Assim, a hora do evento não é ajustada. Para fins de análise, este evento será considerado como o evento anterior número 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | O evento chegou fora de ordem e fora da tolerância de 8 segundos. Assim, o tempo do evento é ajustado ao máximo de tolerância fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Estas configurações podem atrasar a saída do meu trabalho? 

Sim. Por defeito, a política fora de ordem é definida para zero (00 minutos e 00 segundos). Se alterar o padrão, a primeira saída do seu trabalho é adiada por este valor (ou maior). 

Se uma das divisórias das suas entradas não receber eventos, deverá esperar que a sua saída seja adiada pelo valor da política de chegada tardia. Para saber porquê, leia a secção de erro da Partição de Entrada abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vejo mensagens LateInputEvents no meu registo de atividade

Estas mensagens mostram-se a informá-lo de que os eventos chegaram atrasados e são retirados ou ajustados de acordo com a sua configuração. Pode ignorar estas mensagens se tiver configurado a política de chegada tardia adequadamente. 

Exemplo desta mensagem é: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vejo InputPartitionNotProgressing no meu registo de atividade

A sua fonte de entrada (Hub de Evento/Hub IoT) provavelmente tem várias divisórias. O Azure Stream Analytics produz saída para o carimbo de tempo t1 apenas depois de todas as divisórias combinadas serem pelo menos no momento t1. Por exemplo, assuma que a consulta lê a partir de uma partição do centro de eventos que tem duas divisórias. Uma das divisórias, P1, tem eventos até ao tempo t1. A outra partição, P2, tem eventos até ao momento t1 + x. A saída é então produzida até ao tempo t1. Mas se houver uma cláusula de Partição explícita por PartitionId, ambas as divisórias progridem independentemente. 

Quando várias divisórias do mesmo fluxo de entrada são combinadas, a tolerância de chegada tardia é o máximo de tempo que cada partição espera por novos dados. Se houver uma partição no seu Event Hub, ou se o IoT Hub não receber entradas, a linha temporal para essa partição não progride até atingir o limiar de tolerância à chegada tardia. Isto atrasa a sua produção pelo limiar de tolerância à chegada tardia. Nesses casos, pode ver a seguinte mensagem:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem para informá-lo de que pelo menos uma partição na sua entrada está vazia e irá atrasar a sua saída pelo limiar de chegada tardia. Para superar isto, recomenda-se também:  
1. Certifique-se de que todas as divisórias do seu Hub de Evento/IoT Hub recebem entrada. 
2. Utilize a cláusula Partition by PartitionID na sua consulta. 

## <a name="next-steps"></a>Passos seguintes
* [Considerações de processamento de tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
