---
title: Use métricas para monitorizar o Azure IoT Hub Microsoft Docs
description: Como utilizar as métricas do Azure IoT Hub para avaliar e monitorizar a saúde geral dos seus centros de IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: c448d7e5a5e0bea29063930bed3a59a0461b8cf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767618"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>Compreender as métricas do IoT Hub

As métricas IoT Hub dão-lhe informações sobre o estado dos recursos Azure IoT na sua subscrição Azure. As métricas do IoT Hub ajudam-no a avaliar a saúde geral do serviço IoT Hub e os dispositivos que lhe estão ligados. Estas estatísticas voltadas para o utilizador ajudam-no a ver o que se passa com o seu hub IoT e ajudam a realizar análises de causa-raiz em questões sem precisar de contactar o suporte do Azure.

As métricas são ativadas por padrão. Pode ver as métricas do IoT Hub a partir do portal Azure.

> [!NOTE]
> Pode utilizar métricas do IoT Hub para visualizar informações sobre dispositivos [IoT Plug e Play](../iot-pnp/overview-iot-plug-and-play.md) ligados ao seu Hub IoT.

## <a name="how-to-view-iot-hub-metrics"></a>Como ver as métricas do IoT Hub

1. Criar um hub IoT. Pode encontrar instruções sobre como criar um hub IoT no [Enviar telemetria de um dispositivo para guia IoT Hub.](quickstart-send-telemetry-dotnet.md)

2. Abra a lâmina do seu hub IoT. A partir daí, clique em **Métricas.**
   
    ![Screenshot mostrando onde a opção métrica está na página de recursos do IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. A partir da lâmina de métrica, você pode ver as métricas para o seu hub IoT e criar vistas personalizadas das suas métricas. 
   
    ![Screenshot mostrando a página de métricas para IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Pode optar por enviar os seus dados de métricas para um ponto final do Event Hubs ou uma conta de Armazenamento Azure clicando nas **definições de Diagnóstico,** em seguida, **Adicione a definição de diagnóstico**.

   ![Screenshot mostrando onde está o botão de definições de diagnóstico](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas IoT Hub e como usá-las

O IoT Hub fornece várias métricas para lhe dar uma visão geral da saúde do seu hub e do número total de dispositivos conectados. Você pode combinar informações de várias métricas para pintar uma imagem maior do estado do hub IoT. A tabela seguinte descreve as métricas que cada hub IoT rastreia, e como cada métrica se relaciona com o estado geral do hub IoT.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RoteamentoDelivas | Tentativas de entrega de encaminhamento (pré-visualização) | Contagem | Total |Esta é a métrica de entrega de encaminhamento. Utilize as dimensões para identificar o estado de entrega de um ponto final específico ou para uma fonte de encaminhamento específica.| ResourceID,<br>Resultado:<br>RoutingSource,<br>EndpointType,<br>FalhaReasonCategoria,<br>Nome de ponto final<br>*Mais detalhes sobre as dimensões [**aqui.**](#dimensions)* |
|EncaminhamentoDeliveryLatency| Encaminhamento de Latência (pré-visualização) | Milissegundos | Média |Esta é a métrica de latência de entrega de encaminhamento. Utilize as dimensões para identificar a latência de um ponto final específico ou para uma fonte de encaminhamento específica.| ResourceID,<br>RoutingSource,<br>EndpointType,<br>Nome de ponto final<br>*Mais detalhes sobre as dimensões [**aqui.**](#dimensions)*|
|EncaminhamentoDataSizeInBytesDelivered| Tamanho dos dados de entrega de encaminhamento em bytes (pré-visualização)| Bytes | Total |O número total de bytes encaminhados pelo IoT Hub para o ponto final personalizado e o ponto final incorporado. Utilize as dimensões para identificar o tamanho dos dados encaminhados para um ponto final específico ou para uma fonte de encaminhamento específica.| ResourceID,<br>RoutingSource,<br>Tipo de ponto final<br>Nome de ponto final<br>*Mais detalhes sobre as dimensões [**aqui.**](#dimensions)*|
|d2c.telemetria.ingress.<br>allProtocol|Mensagem de telemetria envia tentativas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Nenhum|
|d2c.telemetria.ingress.<br>exito|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhum|
|c2d.commands.egress.<br>completo.sucesso|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Nenhum|
|c2d.commands.egress.<br>abandonar.sucesso|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.<br>rejeitar.sucesso|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D Expiradas (pré-visualização)|Contagem|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Nenhum|
|dispositivos.totalDevices|Total de dispositivos (precotados)|Contagem|Total|Número de dispositivos registados no seu hub IoT|Nenhum|
|dispositivos.connectedDevices.<br>allProtocol|Dispositivos ligados (precotados) |Contagem|Total|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|d2c.telemetria.egress.<br>exito|Encaminhamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Nenhum|
|d2c.telemetria.egress.<br>caiu|Encaminhamento: mensagens de telemetria caídas |Contagem|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Nenhum|
|d2c.telemetria.egress.<br>órfão|Encaminhamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a qualquer consulta de encaminhamento, quando a rota de retorno é desativada.|Nenhum|
|d2c.telemetria.egress.<br>Inválido|Encaminhamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Uma mensagem é incompatível com um ponto final quando o Iot Hub tenta entregar a mensagem a um ponto final e falha com um erro não transitório. As mensagens inválidas não são novamente experimentadas. Este valor não inclui retrações.|Nenhum|
|d2c.telemetria.egress.<br>fallback|Encaminhamento: mensagens entregues para recuo|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Nenhum|
|d2c.endpoints.egress.<br>eventHubs|Encaminhamento: mensagens entregues no Centro de Eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso a pontos finais personalizados do tipo Event Hub. Isto não inclui as rotas de mensagens para o ponto final incorporado (eventos).|Nenhum|
|d2c.endpoints.latência.<br>eventHubs|Encaminhamento: latência da mensagem para o Centro de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens em pontos finais personalizados do tipo Event Hub. Isto não inclui as rotas de mensagens para o ponto final incorporado (eventos).|Nenhum|
|d2c.endpoints.egress.<br>serviceBusQueues|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Nenhum|
|d2c.endpoints.latência.<br>serviceBusQueues|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de fila de autocarros de serviço.|Nenhum|
|d2c.endpoints.egress.<br>serviceBusTopics|Encaminhamento: mensagens entregues no Service Bus Topic|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Nenhum|
|d2c.endpoints.latência.<br>serviceBusTopics|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de ônibus de serviço.|Nenhum|
|d2c.endpoints.egress.<br>builtIn.eventos|Encaminhamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos) e à rota de retorno.|Nenhum|
|d2c.endpoints.latência.<br>builtIn.eventos|Encaminhamento: latência da mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens no ponto final incorporado (mensagens/eventos) e a rota de recuo.|Nenhum|
|d2c.endpoints.egress.<br>storage|Encaminhamento: mensagens entregues no armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.latência.<br>storage|Encaminhamento: latência da mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de armazenamento.|Nenhum|
|d2c.endpoints.egress.<br>armazenamento.bytes|Encaminhamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Nenhum|
|d2c.endpoints.egress.<br>storage.blobs|Encaminhamento: bolhas entregues ao armazenamento|Contagem|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Nenhum|
|EventGridDeliveries|Entregas de Grelha de Eventos (pré-visualização)|Contagem|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|ResourceId,<br/>Resultado:<br/>EventType|
|EventGridLatency|Latência da grelha de eventos (pré-visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,<br/>EventType|
|d2c.twin.read.success|Leituras gémeas bem sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.read.failure|Leituras gémeas falhadas dos dispositivos|Contagem|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Nenhum|
|d2c.twin.read.size|Tamanho de resposta de leituras gémeas de dispositivos|Bytes|Média|O número de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|d2c.twin.update.success|Atualizações gémeas bem sucedidas dos dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|d2c.twin.update.failure|Falhas em duas atualizações a partir de dispositivos|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações gémeas dos dispositivos|Bytes|Média|O tamanho total de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|c2d.methods.success|Invocações de métodos diretos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Nenhum|
|c2d.methods.falha|Invocações de método direto falhadas|Contagem|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhum|
|c2d.methods.requestSize|Tamanho do pedido de invocações de método direto|Bytes|Média|A contagem de todos os pedidos de métodos diretos bem sucedidos.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações do método direto|Bytes|Média|A contagem de todas as respostas bem sucedidas do método direto.|Nenhum|
|c2d.twin.read.success|Gémea bem sucedida lê a partir da parte de trás|Contagem|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Nenhum|
|c2d.twin.read.failure|Gémeo falhado lê do fundo|Contagem|Total|A contagem de todas as leituras gémeas falhadas.|Nenhum|
|c2d.twin.read.size|Tamanho de resposta de leituras gémeas da parte de trás|Bytes|Média|A contagem de todas as leituras gémeas iniciadas com sucesso.|Nenhum|
|c2d.twin.update.success|Atualizações gémeas bem sucedidas a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|
|c2d.twin.update.failure|Falhas em duas atualizações a partir do final|Contagem|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações gémeas a partir da parte de trás|Bytes|Média|O tamanho total de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|
|twinQueries.sucesso|Consultas gémeas bem sucedidas|Contagem|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhum|
|twinQueries.falha|Consultas gémeas falhadas|Contagem|Total|A contagem de todas as consultas gémeas falhadas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado de consultas gémeas|Bytes|Média|O total do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.<br>exito|Criações bem sucedidas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Nenhum|
|jobs.createTwinUpdateJob.<br>falha|Criações falhadas de trabalhos de atualização dupla|Contagem|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Nenhum|
|jobs.createDirectMethodJob.<br>exito|Criações bem sucedidas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Nenhum|
|jobs.createDirectMethodJob.<br>falha|Criações falhadas de trabalhos de invocação de métodos|Contagem|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Nenhum|
|jobs.listJobs.sucesso|Chamadas bem-sucedidas para listar empregos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhum|
|empregos.listJobs.fracasso|Chamadas falhadas para listar empregos|Contagem|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhum|
|jobs.cancelJob.sucesso|Cancelamentos de emprego bem-sucedidos|Contagem|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de empregos falhados|Contagem|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Nenhum|
|jobs.queryJobs.sucesso|Consultas de trabalho bem sucedidas|Contagem|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhum|
|jobs.consultaJobs.falha|Consultas de trabalho falhadas|Contagem|Total|A contagem de todas as chamadas falhadas para consultas.|Nenhum|
|empregos.concluídos|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|empregos.falhou|Empregos falhados|Contagem|Total|A contagem de todos os trabalhos falhados.|Nenhum|
|d2c.telemetria.ingress.<br>sendThrottle|Número de erros de estrangulamento|Contagem|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens utilizadas|Contagem|Média|Número de mensagens totais usadas hoje. Este é um valor acumulado que é reposto para zero às 00:00 UTC todos os dias.|Nenhum|
|dispositivoDataUsage|Total da utilização de dados do dispositivo|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|dispositivoDataUsageV2|Total da utilização dos dados do dispositivo (pré-visualização)|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|totalDeviceCount|Total de dispositivos (pré-visualização)|Contagem|Média|Número de dispositivos registados no seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (pré-visualização)|Contagem|Média|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|configurações|Métricas de configuração|Contagem|Total|Número de operações CRUD totais realizadas para a configuração do dispositivo e implantação IoT Edge, num conjunto de dispositivos-alvo. Isto também inclui o número de operações que modificam o dispositivo twin ou módulo twin devido a estas configurações.|Nenhum|

### <a name="dimensions"></a>Dimensões
As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de encaminhamento fornecem informações por ponto final. Quadro abaixo lista valores possíveis para estas dimensões.

|Dimensão|Valores|
|---|---|
|ResourceID|seu recurso IoT Hub|
|Resultado|exito<br>falha|
|RoutingSource|Mensagens de dispositivo<br>Eventos de Mudança Dupla<br>Eventos de ciclo de vida do dispositivo|
|Tipo de ponto final|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>builtin<br>blobStorage|
|FalhaReasonCategoria|Inválido<br>caiu<br>órfão<br>nulo|
|Nome de ponto final|nome de ponto final|

## <a name="next-steps"></a>Passos seguintes

Agora que viu uma visão geral das métricas do IoT Hub, siga este link para saber mais sobre a gestão do Azure IoT Hub:

* [Configurar os registos de diagnóstico](iot-hub-monitor-resource-health.md)

* [Saiba como resolver o encaminhamento de mensagens de resolução de problemas](troubleshoot-message-routing.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
