---
title: Utilizar métricas para monitorizar o IoT Hub do Azure | Documentos da Microsoft
description: Como utilizar as métricas do IoT Hub do Azure para avaliar e monitorizar o estado de funcionamento geral os hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 8eac70db0c563f102dfa0e3fcece9d4604582cce
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595666"
---
# <a name="understand-iot-hub-metrics"></a>Compreender as métricas do IoT Hub

IoT Hub métricas oferecem-lhe dados melhor sobre o estado dos recursos de IoT do Azure na sua subscrição do Azure. Métricas do IoT Hub permitem-lhe avaliar o estado de funcionamento geral do serviço IoT Hub e dispositivos conectados a ele. Destinada ao utilizador estatísticas são importantes porque eles ajudá-lo a ver o que está acontecendo com problemas de causa raiz seu IoT hub e ajuda sem a necessidade de contactar o suporte do Azure.

As métricas estão ativadas por predefinição. Pode ver as métricas do IoT Hub no portal do Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Como ver as métricas do IoT Hub

1. Crie um hub IoT. Pode encontrar instruções sobre como criar um hub IoT a [enviar telemetria a partir de um dispositivo ao IoT Hub](quickstart-send-telemetry-dotnet.md) guia.

2. Abra o painel do IoT hub. A partir daí, clique em **métricas**.
   
    ![Captura de ecrã que mostra onde a opção de métricas está na página de recursos do IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. No painel métricas, pode ver as métricas para o seu hub IoT e criar vistas personalizadas, as métricas. 
   
    ![Captura de ecrã que mostra a página de métricas para o IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Pode optar por enviar os seus dados de métricas para um ponto de extremidade de Hubs de eventos ou uma conta de armazenamento do Azure ao clicar em **as definições de diagnóstico**, em seguida, **Adicionar definição de diagnóstico**

   ![Captura de ecrã que mostra onde está o botão de definições de diagnóstico](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do IoT Hub e como utilizá-los

O IoT Hub fornece várias métricas para uma visão geral do Estado de funcionamento do seu hub e o número total de dispositivos ligados. Pode combinar informações a partir de várias métricas para pintar uma visão mais ampla do Estado do IoT hub. A tabela seguinte descreve as métricas que controla cada hub IoT e como cada uma se relaciona com o estado geral do IoT hub.

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.Ingress.<br>allProtocol|Tentativas de envio de mensagens de telemetria|Count|Total|Número de mensagens de telemetria do dispositivo-para-cloud tentada a serem enviados ao seu hub IoT|Nenhuma dimensão|
|d2c<br>.telemetry<br>.Ingress<br>.Success|Mensagens de telemetria enviadas|Count|Total|Número de mensagens de telemetria do dispositivo-para-cloud enviado com êxito ao seu hub IoT|Nenhuma dimensão|
|c2d<br>.commands<br>.egress<br>.complete<br>.Success|Comandos concluídos|Count|Total|Número de comandos do cloud-para-dispositivo foi concluída com êxito pelo dispositivo|Nenhuma dimensão|
|c2d<br>.commands<br>.egress<br>.abandon<br>.Success|Comandos abandonados|Count|Total|Número de comandos do cloud-para-dispositivo abandonadas pelo dispositivo|Nenhuma dimensão|
|c2d<br>.commands<br>.egress<br>.reject<br>.Success|Comandos rejeitados|Count|Total|Número de comandos do cloud-para-dispositivo rejeitado pelo dispositivo|Nenhuma dimensão|
|dispositivos<br>.totalDevices|Total de dispositivos (preterido)|Count|Total|Número de dispositivos registados no seu hub IoT|Nenhuma dimensão|
|dispositivos<br>.connectedDevices<br>.allProtocol|Dispositivos ligados (preteridos) |Count|Total|Número de dispositivos ligados ao seu hub IoT|Nenhuma dimensão|
|d2c<br>.telemetry<br>.egress<br>.Success|Encaminhamento: mensagens de telemetria entregues|Count|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos finais através do IoT Hub encaminhamento. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta um para cada entrega concluída com êxito. Se uma mensagem é enviada para o mesmo ponto final várias vezes, esse valor aumenta um para cada entrega concluída com êxito.|Nenhuma dimensão|
|d2c<br>.telemetry<br>.egress<br>.dropped|Encaminhamento: mensagens de telemetria removidas |Count|Total|O número de vezes que as mensagens foram removidas pelo IoT Hub encaminhamento devido a pontos de extremidade inativos. Este valor não conta mensagens entregues a rota de contingência, como mensagens de ignorados não são entregues lá.|Nenhuma dimensão|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Encaminhamento: mensagens de telemetria órfãos |Count|Total|O número de vezes que as mensagens foram órfão pelo encaminhamento do IoT Hub porque eles não correspondam a quaisquer regras de encaminhamento (incluindo a regra de contingência). |Nenhuma dimensão|
|d2c<br>.telemetry<br>.egress<br>.invalid|Encaminhamento: mensagens de telemetria incompatíveis|Count|Total|O número de vezes que o IoT Hub encaminhamento não foi possível entregar mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui tentativas.|Nenhuma dimensão|
|d2c<br>.telemetry<br>.egress<br>.fallback|Encaminhamento: mensagens entregues para contingência|Count|Total|O número de vezes que o IoT Hub encaminhamento entregar mensagens para o ponto final associado a rota de contingência.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.eventHubs|Encaminhamento: mensagens entregues ao Hub de eventos|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para pontos finais do Hub de eventos.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.latency<br>.eventHubs|Encaminhamento: latência da mensagem para o Hub de eventos|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem num ponto de extremidade do Hub de eventos.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.serviceBusQueues|Encaminhamento: mensagens entregues à fila do Service Bus|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para pontos finais de fila do Service Bus.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.latency<br>.serviceBusQueues|Encaminhamento: latência de mensagem de fila do Service Bus|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de extremidade de fila do Service Bus.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.serviceBusTopics|Encaminhamento: mensagens entregues ao tópico do Service Bus|Count|Total|O número de vezes encaminhamento com êxito o IoT Hub entregar mensagens para pontos finais de tópico do Service Bus.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.latency<br>.serviceBusTopics|Encaminhamento: latência da mensagem para o tópico do Service Bus|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de extremidade do tópico do Service Bus.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.builtIn<br>.events|Encaminhamento: mensagens entregues para mensagens/eventos|Count|Total|O número de vezes que o IoT Hub com êxito de encaminhamento entregar mensagens para o ponto final incorporado (mensagens/eventos).|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.latency<br>.builtIn.events|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para o ponto final incorporado (mensagens/eventos).|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.storage|Encaminhamento: mensagens entregues para o armazenamento|Count|Total|O número de vezes encaminhamento com êxito o IoT Hub entregar mensagens para pontos finais de armazenamento.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.latency<br>.storage|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a receção de mensagem para o IoT Hub e entrada de mensagem de telemetria para um ponto de final de armazenamento.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.storage<br>.bytes|Encaminhamento: dados entregues para o armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub encaminhamento entregues aos pontos finais de armazenamento.|Nenhuma dimensão|
|d2c<br>.Endpoints<br>.egress<br>.storage<br>.blobs|Encaminhamento: blobs é entregue ao armazenamento|Count|Total|O número de vezes que o IoT Hub encaminhamento entregue blobs para pontos finais de armazenamento.|Nenhuma dimensão|
|EventGridDeliveries|Entregas de grelha de eventos (pré-visualização)|Count|Total|O número de pedidos para o IoT Hub emitir eventos para o Event Grid. Este número inclui pedidos com êxito ou falhados. Utilize a dimensão de resultado para o número de tipos de resposta diferente. Para ver de onde os pedidos são provenientes, utilize a dimensão de EventType.|Resultado, EventType|
|EventGridLatency|Latência de grelha de eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) entre a receção de eventos para o IoT Hub e entrada de eventos no Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão de EventType para ver a latência de um tipo específico de evento.|EventType|
|d2c<br>.twin<br>.read<br>.Success|Lê a partir do dispositivos duplos com êxito|Count|Total|Contagem de leituras de iniciada pelo dispositivo duplo tudo com êxito.|Nenhuma dimensão|
|d2c<br>.twin<br>.read<br>.failure|Falha de leituras de twin de dispositivos|Count|Total|A contagem de todos os falha leituras iniciada pelo dispositivo duplo.|Nenhuma dimensão|
|d2c<br>.twin<br>.read<br>.size|Tamanho de resposta de leituras de twin de dispositivos|Bytes|Média|A média, Mín e máx. de tudo com êxito iniciada pelo dispositivo duplo leituras.|Nenhuma dimensão|
|d2c<br>.twin<br>.update<br>.Success|Atualizações de duplo com êxito a partir de dispositivos|Count|Total|Contagem de todos os com êxito atualizações de iniciada pelo dispositivo duplo.|Nenhuma dimensão|
|d2c<br>.twin<br>.update<br>.failure|Falha de atualizações de twin de dispositivos|Count|Total|A contagem de todos os falha atualizações iniciadas pelo dispositivo duplo.|Nenhuma dimensão|
|d2c<br>.twin<br>.update<br>.size|Tamanho de atualizações de twin de dispositivos|Bytes|Média|A média, Mín e tamanho máximo de todos os bem-sucedida iniciada pelo dispositivo duplo atualizações.|Nenhuma dimensão|
|c2d<br>.methods<br>.Success|Invocações de método direto com êxito|Count|Total|Contagem de chamadas de método direto tudo com êxito.|Nenhuma dimensão|
|c2d<br>.methods<br>.failure|Falha de invocações de método direto|Count|Total|A contagem de todos os falha chamadas de método direto.|Nenhuma dimensão|
|c2d<br>.methods<br>.requestSize|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, Mín e máx. de bem-sucedida de todos os pedidos de método direto.|Nenhuma dimensão|
|c2d<br>.methods<br>.responseSize|Tamanho de resposta de invocações de método direto|Bytes|Média|A média, Mín e máx. de respostas de método direto tudo com êxito.|Nenhuma dimensão|
|c2d<br>.twin<br>.read<br>.Success|Lê a partir do back-end duplo com êxito|Count|Total|Contagem de leituras de back-end-iniciada duplo tudo com êxito.|Nenhuma dimensão|
|c2d<br>.twin<br>.read<br>.failure|Leituras de duplo com falha de back-end|Count|Total|A contagem de todos os falha leituras duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d<br>.twin<br>.read<br>.size|Tamanho de resposta do duplo lê a partir do back-end|Bytes|Média|A média, Mín e máx. de todos os bem-sucedida iniciada pelo back-end duplo leituras.|Nenhuma dimensão|
|c2d<br>.twin<br>.update<br>.Success|Duplo com êxito as atualizações a partir de back-end|Count|Total|Contagem de todos os com êxito atualizações de duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d<br>.twin<br>.update<br>.failure|Atualizações de duplo com falha de back-end|Count|Total|A contagem de todos os falha atualizações duplo iniciada pelo back-end.|Nenhuma dimensão|
|c2d<br>.twin<br>.update<br>.size|Tamanho de atualizações de duplo de back-end|Bytes|Média|A média, Mín e tamanho máximo de todos os bem-sucedida iniciada pelo back-end duplo atualizações.|Nenhuma dimensão|
|twinQueries<br>.Success|Consultas de gémeos com êxito|Count|Total|Contagem de todas as consultas de gémeos com êxito.|Nenhuma dimensão|
|twinQueries<br>.failure|Consultas de falhados duplo|Count|Total|Contagem de todas as consultas de falhados duplo.|Nenhuma dimensão|
|twinQueries<br>.resultSize|Tamanho de resultados de consultas de gémeos|Bytes|Média|A média, Mín e máx. do tamanho do resultado de todas as consultas de gémeos com êxito.|Nenhuma dimensão|
|tarefas<br>.createTwinUpdateJob<br>.Success|Criações com êxito das tarefas de atualização de duplo|Count|Total|Contagem de criação com êxito todas as tarefas de atualização de duplo.|Nenhuma dimensão|
|tarefas<br>.createTwinUpdateJob<br>.failure|Criações com falhas de tarefas de atualização de duplo|Count|Total|Contagem de todos os falha na criação de tarefas de atualização de duplo.|Nenhuma dimensão|
|tarefas<br>.createDirectMethodJob<br>.Success|Criações com êxito das tarefas de invocação de método|Count|Total|Contagem de criação com êxito todas as tarefas de invocação do método direto.|Nenhuma dimensão|
|tarefas<br>.createDirectMethodJob<br>.failure|Criações com falhas de tarefas de invocação de método|Count|Total|Contagem de todos os falha na criação de tarefas de invocação do método direto.|Nenhuma dimensão|
|tarefas<br>.listJobs<br>.Success|Chamadas com êxito à lista de tarefas|Count|Total|Contagem de todas as chamadas com êxito à lista de tarefas.|Nenhuma dimensão|
|tarefas<br>.listJobs<br>.failure|Falha de chamadas para a lista de tarefas|Count|Total|Contagem de todas as chamadas falhadas para lista de tarefas.|Nenhuma dimensão|
|tarefas<br>.cancelJob<br>.Success|Cancelamentos de tarefas com êxito|Count|Total|Contagem de todas as chamadas com êxito para cancelar uma tarefa.|Nenhuma dimensão|
|tarefas<br>.cancelJob<br>.failure|Cancelamentos de tarefas com falhas|Count|Total|Contagem de todas as chamadas falhadas para cancelar uma tarefa.|Nenhuma dimensão|
|tarefas<br>.queryJobs<br>.Success|Consultas de tarefa concluída com êxito|Count|Total|Contagem de todas as chamadas com êxito para tarefas de consulta.|Nenhuma dimensão|
|tarefas<br>.queryJobs<br>.failure|Consultas de tarefa falhadas|Count|Total|Contagem de todas as chamadas falhadas para tarefas de consulta.|Nenhuma dimensão|
|tarefas<br>.completed|Trabalhos concluídos|Count|Total|Contagem de todas as tarefas concluídas.|Nenhuma dimensão|
|tarefas<br>.failed|Tarefas falhadas|Count|Total|Contagem de todas as tarefas falhadas.|Nenhuma dimensão|
|d2c<br>.telemetry<br>.Ingress<br>.sendThrottle|Número de erros de limitação|Count|Total|Limita o número de erros de limitação devido ao débito de dispositivo|Nenhuma dimensão|
|dailyMessage<br>QuotaUsed|Número total de mensagens utilizada|Count|Média|Número de total de mensagens usados hoje em dia. Este é um valor cumulativo que é reposto a zero 00:00 UTC todos os dias.|Nenhuma dimensão|
|deviceDataUsage|Utilização de dados do total de dispositivos|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhuma dimensão|
|totalDeviceCount|Total de dispositivos (pré-visualização)|Count|Média|Número de dispositivos registados no seu hub IoT|Nenhuma dimensão|
|Ligado<br>DeviceCount|Dispositivos ligados (pré-visualização)|Count|Média|Número de dispositivos ligados ao seu hub IoT|Nenhuma dimensão|
|Configurações|Métricas de configuração|Count|Total|Métricas para operações de configuração|Nenhuma dimensão|

## <a name="next-steps"></a>Passos Seguintes

Agora que já viu uma descrição geral das métricas do IoT Hub, siga esta ligação para saber mais sobre como gerir o IoT Hub do Azure:

* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
