---
title: Usar métricas para monitorar o Hub IoT do Azure | Microsoft Docs
description: Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral de seus hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705739"
---
# <a name="understand-iot-hub-metrics"></a>Entender as métricas do Hub IoT

As métricas do Hub IoT fornecem dados melhores sobre o estado dos recursos de IoT do Azure em sua assinatura do Azure. As métricas do Hub IoT permitem avaliar a integridade geral do serviço do Hub IoT e os dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque ajudam você a ver o que está acontecendo com o Hub IoT e a ajudar a causar problemas de causa raiz sem a necessidade de contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do Hub IoT no portal do Azure.

> [!NOTE]
> Você pode usar as métricas do Hub IoT para exibir informações sobre dispositivos IoT Plug and Play conectados ao seu hub IoT. Os dispositivos IoT Plug and Play fazem parte da [Visualização pública do iot plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Como exibir métricas do Hub IoT

1. Crie um hub IoT. Você pode encontrar instruções sobre como criar um hub IoT no guia [Enviar telemetria de um dispositivo para o Hub IOT](quickstart-send-telemetry-dotnet.md) .

2. Abra a folha do Hub IoT. A partir daí, clique em **métricas**.
   
    ![Captura de tela mostrando onde a opção de métrica está na página de recursos do Hub IoT](./media/iot-hub-metrics/enable-metrics-1.png)

3. Na folha métricas, você pode exibir as métricas para o Hub IoT e criar exibições personalizadas de suas métricas. 
   
    ![Captura de tela mostrando a página de métricas para o Hub IoT](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de hubs de eventos ou uma conta de armazenamento do Azure clicando em **configurações de diagnóstico**e, em seguida, **Adicionar configuração de diagnóstico**

   ![Captura de tela mostrando onde o botão Configurações de diagnóstico é](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las

O Hub IoT fornece várias métricas para fornecer uma visão geral da integridade do seu hub e o número total de dispositivos conectados. Você pode combinar informações de várias métricas para pintar uma imagem maior do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica está relacionada ao status geral do Hub IoT.

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Nenhuma|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Nenhuma|
|c2d.commands.egress.complete.success|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhuma|
|c2d.commands.egress.abandon.success|C2D mensagens abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhuma|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhuma|
|C2DMessagesExpired|C2D mensagens expiradas (visualização)|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhuma|
|devices.totalDevices|Total de dispositivos (preteridos)|Contagem|Total|Número de dispositivos registrados no Hub IoT|Nenhuma|
|devices.connectedDevices.allProtocol|Dispositivos ligados (preteridos) |Contagem|Total|Número de dispositivos conectados ao seu hub IoT|Nenhuma|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Nenhuma|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria eliminadas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Nenhuma|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Nenhuma|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Nenhuma|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Nenhuma|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Nenhuma|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Nenhuma|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagem para a Fila do Service Bus|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Nenhuma|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Nenhuma|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos).|Nenhuma|
|d2c.endpoints.latency.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Nenhuma|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Nenhuma|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Nenhuma|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento (https://aka.ms/ioteventgrid).|Identificação<br/>Disso<br/>EventType|
|EventGridLatency|Latência da grade de eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|Identificação<br/>EventType|
|d2c.twin.read.success|Leituras de entrelaçamento bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|d2c.twin.read.failure|Leituras duplas com falhas de dispositivos|Contagem|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Nenhuma|
|d2c.twin.read.size|Tamanho da resposta de leituras duplas de dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|d2c.twin.update.success|Atualizações de atualização com êxito de dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma|
|d2c.twin.update.failure|Atualizações duplas com falhas dos dispositivos|Contagem|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Nenhuma|
|d2c.twin.update.size|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma|
|C2D. Methods. Success|Invocações de método diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma|
|c2d.methods.failure|Invocações de método direto com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falha.|Nenhuma|
|c2d.methods.requestSize|Pedir tamanho de invocações de método direto|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhuma|
|c2d.methods.responseSize|Tamanho da resposta de invocações de método direto|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhuma|
|c2d.twin.read.success|Leituras de cópia com êxito do back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas.|Nenhuma|
|c2d.twin.read.failure|Leituras duplas com falhas de back-end|Contagem|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Nenhuma|
|c2d.twin.read.size|Tamanho da resposta de leituras duplas de back-end|Bytes|Média|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Nenhuma|
|c2d.twin.update.success|Atualizações duplas com êxito do back-end|Contagem|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma|
|c2d.twin.update.failure|Atualizações duplas com falhas do back-end|Contagem|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Nenhuma|
|c2d.twin.update.size|Tamanho das atualizações de cópia do back-end|Bytes|Média|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma|
|twinQueries.success|Consultas de entrelaçamento bem-sucedidas|Contagem|Total|A contagem de todas as consultas de myup com êxito.|Nenhuma|
|twinQueries.failure|Consultas duplas com falhas|Contagem|Total|A contagem de todas as consultas de entrelaçamento com falha.|Nenhuma|
|twinQueries.resultSize|Tamanho do resultado de consultas de entrelaçamento|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma|
|jobs.createTwinUpdateJob.success|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Contagem|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Nenhuma|
|jobs.createTwinUpdateJob.failure|Criações com falhas de tarefas de atualização duplas|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Nenhuma|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método com êxito|Contagem|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Nenhuma|
|jobs.createDirectMethodJob.failure|Criações com falhas de tarefas de invocação de método|Contagem|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Nenhuma|
|jobs.listJobs.success|Chamadas com êxito para listar trabalhos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Nenhuma|
|jobs.listJobs.failure|Chamadas com falhas para listar tarefas|Contagem|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Nenhuma|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Nenhuma|
|jobs.cancelJob.failure|Cancelamentos de tarefas com falhas|Contagem|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Nenhuma|
|jobs.queryJobs.success|Consultas de trabalho com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Nenhuma|
|jobs.queryJobs.failure|Consultas de tarefa com falhas|Contagem|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Nenhuma|
|jobs.completed|Tarefas concluídas|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhuma|
|jobs.failed|Tarefas com falhas|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhuma|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Nenhuma|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje. Esse é um valor cumulativo que é redefinido para zero às 00:00 UTC todos os dias.|Nenhuma|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma|
|deviceDataUsageV2|Uso total de dados do dispositivo (versão prévia)|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma|
|totalDeviceCount|Total de dispositivos (visualização)|Contagem|Média|Número de dispositivos registrados no Hub IoT|Nenhuma|
|connectedDeviceCount|Dispositivos ligados (pré-visualização)|Contagem|Média|Número de dispositivos conectados ao seu hub IoT|Nenhuma|
|Figura|Métricas de Configuração|Contagem|Total|Métricas para operações de configuração|Nenhuma|

## <a name="next-steps"></a>Passos seguintes

Agora que você já viu uma visão geral das métricas do Hub IoT, siga este link para saber mais sobre como gerenciar o Hub IoT do Azure:

* [Configurar registos de diagnóstico](iot-hub-monitor-resource-health.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)

* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
