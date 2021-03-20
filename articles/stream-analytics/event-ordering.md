---
title: Configurar políticas de encomendas de eventos para Azure Stream Analytics
description: Este artigo descreve como configurar até mesmo encomendar definições no Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98014300"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurar políticas de encomendas de eventos para Azure Stream Analytics

Este artigo descreve como configurar e usar políticas de eventos de chegada tardia e fora de ordem no Azure Stream Analytics. Estas políticas são aplicadas apenas quando utiliza a cláusula [TIMETAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) na sua consulta, e são apenas aplicadas para fontes de entrada na nuvem.

## <a name="event-time-and-arrival-time"></a>Hora do evento e hora de chegada

O seu trabalho stream Analytics pode processar eventos com base na hora do *evento* ou *na hora de chegada.* **O tempo de evento/aplicação** é o tempo de carregamento presente na carga útil do evento (quando o evento foi gerado). **A hora de chegada** é a data de chegada quando o evento foi recebido na fonte de entrada (Event Hubs/IoT Hub/Blob armazenamento). 

Por predefinição, o Stream Analytics processa os eventos até à *hora de chegada,* mas pode optar por processar eventos por *tempo de evento,* utilizando a cláusula [TIMETAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) na sua consulta. As políticas de chegada tardia e fora de encomenda só são aplicáveis se processar eventos por hora do evento. Considere os requisitos de latência e correção para o seu cenário quando configurar estas definições. 

## <a name="what-is-late-arrival-policy"></a>O que é a política de chegada tardia?

Às vezes os eventos chegam atrasados por várias razões. Por exemplo, um evento que chegue 40 segundos atrasado terá tempo de evento = 00:10:00 e hora de chegada = 00:10:40. Se definir a política de chegada tardia para 15 segundos, qualquer evento que chegue mais tarde de 15 segundos será largado (não processado pela Stream Analytics) ou terá o seu tempo de evento ajustado. No exemplo acima, à medida que o evento chegou 40 segundos atrasado (mais do que o conjunto de políticas), o seu tempo de evento será ajustado ao máximo da política de chegada tardia 00:10:25 (hora de chegada - valor da política de chegada tardia). A política de chegada tardia por defeito é de 5 segundos.

## <a name="what-is-out-of-order-policy"></a>O que é a política fora de ordem? 

O evento também pode chegar fora de ordem. Após o tempo do evento ser ajustado com base na política de chegada tardia, também pode optar por largar ou ajustar automaticamente eventos que estão fora de encomenda. Se definir esta política em 8 segundos, quaisquer eventos que cheguem fora de ordem mas dentro da janela de 8 segundos são reordenados pela hora do evento. Os eventos que chegam mais tarde serão abandonados ou ajustados ao valor máximo da política fora de ordem. A política fora de ordem por defeito é de 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajuste ou largue eventos

Se os eventos chegarem tarde ou fora de ordem com base nas políticas configuradas, pode deixar cair tais eventos (não processados pelo Stream Analytics) ou ter o seu tempo de evento ajustado.

Vejamos um exemplo destas políticas em ação.
<br> **Política de chegada tardia:** 15 segundos
<br> **Política fora de ordem:** 8 segundos

| Evento Nº. | Hora do evento | Hora da Chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | O evento chegou tarde e fora do nível de tolerância. Assim, o tempo do evento é ajustado para a tolerância máxima de chegada tardia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | O evento chegou tarde, mas dentro do nível de tolerância. Assim, o tempo do evento não é ajustado.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | O evento chegou a tempo. Não é preciso ajustamento.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | O evento chegou fora de ordem, mas com a tolerância de 8 segundos. Então, o tempo do evento não é ajustado. Para fins analíticos, este evento será considerado como o evento anterior número 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | O evento chegou fora de ordem e tolerância externa de 8 segundos. Assim, o tempo do evento é ajustado ao máximo de tolerância fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Estas definições podem atrasar a saída do meu trabalho? 

Sim. Por predefinição, a política fora de ordem está definida para zero (00 minutos e 00 segundos). Se alterar o padrão, a primeira saída do seu trabalho é retardada por este valor (ou maior). 

Se uma das divisórias das suas entradas não receber eventos, deve esperar que a sua saída seja adiada pelo valor da política de chegada tardia. Para saber porquê, leia a secção de erro inputPartition abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vejo mensagens LateInputEvents no meu registo de atividades

Estas mensagens são mostradas para informá-lo que os eventos chegaram tarde e são largados ou ajustados de acordo com a sua configuração. Pode ignorar estas mensagens se tiver configurado a política de chegada tardia adequadamente. 

Exemplo desta mensagem é: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vejo inputPartitionNotProgressing no meu registo de atividades

A sua fonte de entrada (Event Hub/IoT Hub) provavelmente tem várias divisórias. O Azure Stream Analytics produz a saída para o carimbo de tempo t1 apenas depois de todas as divisórias que são combinadas serem pelo menos no tempo t1. Por exemplo, assuma que a consulta lê a partir de uma partição do centro de eventos que tem duas divisórias. Uma das divisórias, P1, tem eventos até à hora t1. A outra divisória, P2, tem eventos até à hora t1 + x. A saída é então produzida até ao tempo t1. Mas se houver uma partição explícita por cláusula PartitionId, ambas as divisórias progridem independentemente. 

Quando se combinam várias divisições do mesmo fluxo de entrada, a tolerância tardia à chegada é o máximo de tempo que cada partição espera por novos dados. Se houver uma partição no seu Event Hub, ou se o IoT Hub não receber entradas, a linha temporal para essa partição não progride até atingir o limiar de tolerância de chegada tardio. Isto atrasa a sua saída pelo limiar de tolerância à chegada tardia. Nesses casos, pode ver a seguinte mensagem:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem para informá-lo de que pelo menos uma divisória na sua entrada está vazia e irá atrasar a sua saída até ao limiar de chegada tardio. Para superar isto, recomenda-se:  
1. Certifique-se de que todas as divisórias do seu Centro de Eventos/IoT Hub recebem entrada. 
2. Utilize a cláusula Partition by PartitionID na sua consulta. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Por que vejo um atraso de 5 segundos, mesmo quando a minha política de chegada tardia está definida para 0?
Isto acontece quando há uma partição de entrada que nunca recebeu qualquer entrada. Pode verificar as métricas de entrada por partição para validar este comportamento. 

Quando uma partição não tem dados para mais do que o limiar de chegada tardio configurado, a análise de fluxo avança o tempo de aplicação como explicado na secção de considerações de encomenda de eventos. Isto requer a hora estimada de chegada. Se a partição nunca teve dados, a análise do fluxo estima a hora de chegada como *hora local - 5 segundos*. Devido a estas divisórias que nunca tiveram dados poderiam mostrar um atraso de 5 segundos.  

## <a name="next-steps"></a>Passos seguintes
* [Considerações de processamento de tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis em Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)