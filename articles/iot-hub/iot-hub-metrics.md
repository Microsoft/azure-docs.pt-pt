---
title: Use métricas para monitorizar o Hub Azure IoT [ Hub] Microsoft Docs
description: Como utilizar as métricas do Hub Azure IoT para avaliar e monitorizar a saúde geral dos seus hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284437"
---
# <a name="understand-iot-hub-metrics"></a>Compreender as métricas do IoT Hub

As métricas do IoT Hub dão-lhe melhores dados sobre o estado dos recursos Azure IoT na sua subscrição Azure. As métricas do IoT Hub permitem avaliar a saúde geral do serviço IoT Hub e os dispositivos ligados ao mesmo. As estatísticas orientadas para o utilizador são importantes porque ajudam-no a ver o que se passa com o seu hub IoT e ajudam a causar problemas de causa sem precisar de contactar o suporte do Azure.

As métricas são ativadas por defeito. Pode ver as métricas do IoT Hub do portal Azure.

> [!NOTE]
> Pode utilizar métricas IoT Hub para visualizar informações sobre dispositivos IoT Plug e Play ligados ao seu Hub IoT. Os dispositivos IoT Plug and Play fazem parte da [pré-visualização pública IoT Plug e Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Como ver as métricas do Hub IoT

1. Criar um centro de ioT. Pode encontrar instruções sobre como criar um hub IoT na [telemetria Enviar de um dispositivo para guia IoT Hub.](quickstart-send-telemetry-dotnet.md)

2. Abra a lâmina do seu centro de IoT. A partir daí, clique em **Métricas**.
   
    ![Screenshot mostrando onde a opção métricaestá na página de recursos do Hub IoT](./media/iot-hub-metrics/enable-metrics-1.png)

3. A partir da lâmina métrica, você pode ver as métricas para o seu hub IoT e criar vistas personalizadas das suas métricas. 
   
    ![Screenshot mostrando a página métrica para IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Pode optar por enviar os seus dados de métricas para um ponto final do Event Hubs ou uma conta de Armazenamento Azure clicando em **definições**de Diagnóstico, em seguida, **Adicionar definição de diagnóstico**

   ![Screenshot mostrando onde o botão de definições de diagnóstico é](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las

O IoT Hub fornece várias métricas para lhe dar uma visão geral da saúde do seu hub e do número total de dispositivos conectados. Você pode combinar informação de várias métricas para pintar uma imagem maior do estado do centro ioT. A tabela seguinte descreve as métricas de cada hub IoT, e como cada métrica se relaciona com o estado geral do hub IoT.

|Métrica|Nome a apresentar de métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Mensagem de telemetria enviar tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-para-nuvem tentou ser enviada para o seu hub IoT|Nenhum|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens cloud-to-device concluídas com sucesso pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens cloud-to-device rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens cloud-to-device expiradas|Nenhum|
|devices.totalDevices|Total de dispositivos (depreciados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Nenhum|
|devices.connectedDevices.allProtocol|Dispositivos conectados (depreciados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|d2c.telemetry.egress.success|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso a todos os pontos finais usando o encaminhamento do IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta em um para cada entrega bem sucedida. Se uma mensagem for entregue ao mesmo ponto final várias vezes, este valor aumenta por um para cada entrega bem sucedida.|Nenhum|
|d2c.telemetry.egress.dropped|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram retiradas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de recuo, uma vez que as mensagens deixadas não são entregues lá.|Nenhum|
|d2c.telemetry.egress.orphaned|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de mensagens foi órfão pelo encaminhamento do IoT Hub porque não correspondia a nenhuma regra de encaminhamento (incluindo a regra do recuo). |Nenhum|
|d2c.telemetry.egress.invalid|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não entregou mensagens devido a uma incompatibilidade com o ponto final. Este valor não inclui repetições.|Nenhum|
|d2c.telemetry.egress.fallback|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o ioT Hub encaminha-se entregou mensagens ao ponto final associado à rota de recuo.|Nenhum|
|d2c.endpoints.egress.eventHubs|Encaminhamento: mensagens entregues ao Event Hub|Contagem|Total|O número de vezes que o IoT Hub envia mensagens com sucesso entregou mensagens aos pontos finais do Event Hub.|Nenhum|
|d2c.endpoints.latency.eventHubs|Encaminhamento: latência de mensagens para O Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a entrada de mensagens num ponto final do Event Hub.|Nenhum|
|d2c.endpoints.egress.serviceBusQueues|Encaminhamento: mensagens entregues à Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o ioT Hub encaminha-se com sucesso entregou mensagens aos pontos finais da fila do Service Bus.|Nenhum|
|d2c.endpoints.latency.serviceBusQueues|Encaminhamento: latência de mensagens para fila de autocarros de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final da fila do Autocarro de Serviço.|Nenhum|
|d2c.endpoints.egress.serviceBusTopics|Encaminhamento: mensagens entregues ao Tópico do Autocarro de Serviço|Contagem|Total|O número de vezes que o IoT Hub encaminha com sucesso entregou mensagens aos pontos finais do tópico do Bus de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusTopics|Encaminhamento: latência de mensagens para tópico de ônibus de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final do tópico do Bus de Serviço.|Nenhum|
|d2c.endpoints.egress.builtIn.events|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o ioT Hub envia mensagens com sucesso entregou mensagens ao ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.latency.builtIn.events|Encaminhamento: latência de mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando no ponto final incorporado (mensagens/eventos).|Nenhum|
|d2c.endpoints.egress.storage|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o ioT Hub encaminha com sucesso entregou mensagens para pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.latency.storage|Encaminhamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens no IoT Hub e a mensagem de telemetria entrando num ponto final de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub encaminhamento entregue em pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas aos pontos finais de armazenamento.|Nenhum|
|Entregas eventgrid|Entregas da Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos do IoT Hub publicados na Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão do Tipo de Evento mostra o tipo de evento (https://aka.ms/ioteventgrid).|ResourceId,<br/>Resultado,<br/>Tipo de evento|
|EventGridLatency|Latência da Grelha de Eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado até quando o evento foi publicado para Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,<br/>Tipo de evento|
|d2c.twin.read.success|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.read.failure|Leituras gémeas falhadas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhado.|Nenhum|
|d2c.twin.read.size|Tamanho da resposta das leituras gémeas dos dispositivos|Bytes|Média|A média, min e máximo de todas as leituras gémeas bem sucedidas.|Nenhum|
|d2c.twin.update.success|Atualizações gémeas bem-sucedidas a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.failure|Atualizações duplas falhadas de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|c2d.methods.success.success|Invocações de método direto bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidas.|Nenhum|
|c2d.methods.failure|Invocações de método direto falhado|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhum|
|c2d.methods.requestSize|Solicitar dimensão das invocações do método direto|Bytes|Média|A média, min e máximo de todos os pedidos de método direto bem sucedidos.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A média, min e máximo de todas as respostas de método direto bem sucedidas.|Nenhum|
|c2d.twin.read.success|Twin de sucesso lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas bem sucedidas.|Nenhum|
|c2d.twin.read.failure|Gémeo falhado lê a partir de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas no final.|Nenhum|
|c2d.twin.read.size|Tamanho da resposta de leituras gémeas da parte de trás|Bytes|Média|A média, min, e o máximo de todas as leituras gémeas bem sucedidas.|Nenhum|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|c2d.twin.update.failure|Atualizações gémeas falhadas a partir de trás|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações gémeas a partir de trás|Bytes|Média|O tamanho médio, min e máximo de todas as atualizações gémeas iniciadas no back-end.|Nenhum|
|twinQueries.success|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhum|
|twinQueries.failure|Perguntas gémeas falhadas|Contagem|Total|A contagem de todas as perguntas gémeas falhadas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado das consultas gémeas|Bytes|Média|A média, min, e o máximo do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.success|Criações bem sucedidas de empregos de atualização dupla|Contagem|Total|A contagem de toda a criação bem sucedida de empregos de atualização dupla.|Nenhum|
|jobs.createTwinUpdateJob.failure|Criações falhadas de empregos de atualização dupla|Contagem|Total|A contagem de todos os empregos falhados de atualização dupla.|Nenhum|
|jobs.createDirectMethodJob.success|Criações bem sucedidas de empregos de invocação de métodos|Contagem|Total|A contagem de todas as criações bem sucedidas de empregos de invocação de métodos diretos.|Nenhum|
|jobs.createDirectMethodJob.failure|Criações falhadas de empregos de invocação de métodos|Contagem|Total|A contagem de todos os empregos de invocação de métodos diretos.|Nenhum|
|jobs.listJobs.success|Chamadas bem sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhum|
|jobs.listJobs.failure|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhum|
|jobs.cancelJob.success|Cancelamentos de emprego bem sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um trabalho.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de emprego falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um trabalho.|Nenhum|
|jobs.queryJobs.success|Consultas de emprego bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhum|
|jobs.queryJobs.failure|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para trabalhos de consulta.|Nenhum|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|jobs.failed|Empregos falhados|Contagem|Total|A contagem de todos os empregos falhados.|Nenhum|
|d2c.telemetry.ingress.sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido a aceleração de entrada do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens utilizadas|Contagem|Média|Número total de mensagens usadas hoje. Este é um valor cumulativo que é reposto para zero às 00:00 UTC todos os dias.|Nenhum|
|deviceDataUsage|Utilização total dos dados do dispositivo|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhum|
|deviceDataUsageV2|Utilização total de dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para quaisquer dispositivos ligados ao IotHub|Nenhum|
|totaldeDispositivoCount|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|configurações|Métricas de Configuração|Contagem|Total|Métricas para Operações de Configuração|Nenhum|

## <a name="next-steps"></a>Passos Seguintes

Agora que viu uma visão geral das métricas do IoT Hub, siga este link para saber mais sobre a gestão do Azure IoT Hub:

* [Configurar registos de diagnóstico](iot-hub-monitor-resource-health.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)

* [Implementação de IA para dispositivos de borda com Borda Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)
