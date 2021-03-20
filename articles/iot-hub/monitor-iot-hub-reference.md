---
title: Referência de dados do Azure IoT Hub
description: Material de referência importante necessário quando monitoriza o Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570966"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Referência de dados do Azure IoT Hub

Consulte [o Monitor Azure IoT Hub](monitor-iot-hub.md) para obter detalhes sobre a recolha e análise de dados de monitorização do Azure IoT Hub.

## <a name="metrics"></a>Métricas

Esta secção lista todas as métricas da plataforma recolhidas automaticamente para O Azure IoT Hub. O espaço de nome do fornecedor de recursos para métricas IoT Hub é **o Microsoft.Devices** e o tipo Namespace é **IoTHubs**.

As subsecções seguintes desframam as métricas da plataforma IoT Hub por categoria geral e listam-nas pelo nome de exibição com o que aparecem no portal Azure. A informação é também fornecida relevante para as métricas que aparecem em cada subsecção.

Também pode encontrar uma única tabela que lista todas as métricas da plataforma IoT Hub por nome métrico sob [microsoft.Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs) na documentação do Azure Monitor. Esteja ciente de que esta tabela não fornece algumas das informações, como [agregações suportadas](#supported-aggregations) para algumas métricas, disponíveis neste artigo.

Para conhecer as métricas suportadas por outros serviços Azure, consulte [métricas suportadas com o Azure Monitor.](../azure-monitor/essentials/metrics-supported.md)

**Tópicos nesta secção**

- [Referência de dados do Azure IoT Hub](#monitoring-azure-iot-hub-data-reference)
  - [Métricas](#metrics)
    - [Agregações apoiadas](#supported-aggregations)
    - [Métricas de comando de nuvem para dispositivo](#cloud-to-device-command-metrics)
    - [Nuvem para dispositivo métodos diretos métricas](#cloud-to-device-direct-methods-metrics)
    - [Nuvem para dispositivo métricas de operações gémeas](#cloud-to-device-twin-operations-metrics)
    - [Métricas de configurações](#configurations-metrics)
    - [Métricas diárias de quota](#daily-quota-metrics)
    - [Métricas do dispositivo](#device-metrics)
    - [Métricas de telemetria do dispositivo](#device-telemetry-metrics)
    - [Dispositivo para cloud twin operações métricas](#device-to-cloud-twin-operations-metrics)
    - [Métricas da grelha de eventos](#event-grid-metrics)
    - [Métricas de emprego](#jobs-metrics)
    - [Métricas de encaminhamento](#routing-metrics)
    - [Métricas de consulta dupla](#twin-query-metrics)
  - [Dimensões métricas](#metric-dimensions)
  - [Registos do recurso](#resource-logs)
    - [Ligações](#connections)
    - [Telemetria do dispositivo](#device-telemetry)
    - [Comandos da cloud para dispositivo](#cloud-to-device-commands)
    - [Operações de identidade do dispositivo](#device-identity-operations)
    - [Operações de carregamento de ficheiros](#file-upload-operations)
    - [Rotas](#routes)
    - [Operações gémeas dispositivo-a-nuvem](#device-to-cloud-twin-operations)
    - [Operações gémeas nuvem-para-dispositivo](#cloud-to-device-twin-operations)
    - [Consultas gémeas](#twin-queries)
    - [Operações de tarefas](#jobs-operations)
    - [Métodos Diretos](#direct-methods)
    - [Rastreio distribuído (pré-visualização)](#distributed-tracing-preview)
      - [Troncos IoT Hub D2C (dispositivo-nuvem)](#iot-hub-d2c-device-to-cloud-logs)
      - [Troncos de entrada do IoT Hub](#iot-hub-ingress-logs)
      - [Troncos de saída do IoT Hub](#iot-hub-egress-logs)
    - [Configurações](#configurations)
    - [Streams de dispositivos (pré-visualização)](#device-streams-preview)
  - [Tabelas de registos do monitor Azure](#azure-monitor-logs-tables)
  - [Ver Também](#see-also)

### <a name="supported-aggregations"></a>Agregações apoiadas

A coluna **Tipo de Agregação** em cada tabela corresponde à agregação predefinitiva que é utilizada quando a métrica é selecionada para um gráfico ou alerta.

   ![Screenshot mostrando agregação para métricas](./media/monitor-iot-hub-reference/aggregation-type.png)

Para a maioria das métricas, todos os tipos de agregação são válidos; no entanto, para as métricas de contagem, aqueles com um valor de coluna **unitária** do **Conde,** apenas algumas agregações são válidas. As métricas de contagem podem ser um de dois tipos:

* Para as métricas de contagem **de pontos únicos,** o IoT Hub regista um único ponto de dados -- essencialmente um 1 - sempre que ocorre a operação medida. O Azure Monitor em seguida, soma estes pontos de dados sobre a granularidade especificada. Exemplos de métricas **de ponto único** são as mensagens de *telemetria enviadas* e *as entregas de mensagens C2D concluídas*. Para estas métricas, o único tipo de agregação relevante é o Total (Soma). O portal permite-lhe escolher o mínimo, máximo e médio; no entanto, estes valores serão sempre 1.

* Para as métricas de contagem **instantânea,** o IoT Hub regista uma contagem total quando ocorre a operação medida. Atualmente, existem três métricas **Snapshot** emitidas pelo IoT Hub: *Número total de mensagens* *utilizadas, dispositivos totais (pré-visualização)* e *dispositivos conectados (pré-visualização)*. Porque estas métricas apresentam uma quantidade "total" cada vez que são emitidas, resumindo-as sobre a granularidade especificada não faz sentido. O Azure Monitor limita-o a selecionar a média, o mínimo e o máximo para o tipo de agregação para estas métricas.

### <a name="cloud-to-device-command-metrics"></a>Métricas de comando de nuvem para dispositivo

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Mensagens C2D Expiradas (pré-visualização)|C2DMessagesExpired|de palavras|Total|Número de mensagens de nuvem-para-dispositivo expiradas|Nenhum|
|Entregas de mensagens C2D concluídas|c2d.commands.egress.complete.success|de palavras|Total|Número de entregas de mensagens nuvem-para-dispositivo concluídas com sucesso pelo dispositivo|Nenhum|
|Mensagens C2D abandonadas|c2d.commands.egress.abandon.success|de palavras|Total|Número de mensagens cloud-to-device abandonadas pelo dispositivo|Nenhum|
|Mensagens C2D rejeitadas|c2d.commands.egress.reject.success|de palavras|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="cloud-to-device-direct-methods-metrics"></a>Nuvem para dispositivo métodos diretos métricas

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Invocações de método direto falhadas|c2d.methods.falha|de palavras|Total|A contagem de todas as chamadas de métodos diretos falhadas.|Nenhum|
|Tamanho do pedido de invocações de método direto|c2d.methods.requestSize|Bytes|Média|A contagem de todos os pedidos de métodos diretos bem sucedidos.|Nenhum|
|Tamanho da resposta das invocações do método direto|c2d.methods.responseSize|Bytes|Média|A contagem de todas as respostas bem sucedidas do método direto.|Nenhum|
|Invocações de métodos diretos bem-sucedidas|c2d.methods.success|de palavras|Total|A contagem de todas as chamadas de métodos diretos bem sucedidos.|Nenhum|

Para métricas com um valor **unitário** de **agregação** de count apenas é válida a agregação total (soma). Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="cloud-to-device-twin-operations-metrics"></a>Nuvem para dispositivo métricas de operações gémeas

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Gémeo falhado lê do fundo|c2d.twin.read.failure|de palavras|Total|A contagem de todas as leituras gémeas falhadas.|Nenhum|
|Falhas em duas atualizações a partir do final|c2d.twin.update.failure|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pela parte de trás.|Nenhum|
|Tamanho de resposta de leituras gémeas da parte de trás|c2d.twin.read.size|Bytes|Média|A contagem de todas as leituras gémeas iniciadas com sucesso.|Nenhum|
|Tamanho das atualizações gémeas a partir da parte de trás|c2d.twin.update.size|Bytes|Média|O tamanho total de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|
|Gémea bem sucedida lê a partir da parte de trás|c2d.twin.read.success|de palavras|Total|A contagem de todas as leituras gémeas iniciadas com sucesso.|Nenhum|
|Atualizações gémeas bem sucedidas a partir do final|c2d.twin.update.success|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas com sucesso.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="configurations-metrics"></a>Métricas de configurações

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Métricas de configuração|configurações|de palavras|Total|Número de operações CRUD totais realizadas para a configuração do dispositivo e implantação IoT Edge, num conjunto de dispositivos-alvo. Isto também inclui o número de operações que modificam o dispositivo twin ou módulo twin devido a estas configurações.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="daily-quota-metrics"></a>Métricas diárias de quota

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Total da utilização de dados do dispositivo|dispositivoDataUsage|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|Total da utilização dos dados do dispositivo (pré-visualização)|dispositivoDataUsageV2|Bytes|Total|Bytes transferidos de e para qualquer dispositivo ligado ao IotHub|Nenhum|
|Número total de mensagens utilizadas|dailyMessageQuotaUsed|de palavras|Média|Número de mensagens totais usadas hoje. Este é um valor acumulado que é reposto para zero às 00:00 UTC todos os dias.|Nenhum|

Para *o número total de mensagens utilizadas*, apenas são suportadas agregações mínimas, máximas e médias. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="device-metrics"></a>Métricas do dispositivo

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Total de dispositivos (precotados)|dispositivos.totalDevices|de palavras|Total|Número de dispositivos registados no seu hub IoT|Nenhum|
|Dispositivos ligados (precotados) |dispositivos.connectedDevices.allProtocol|de palavras|Total|Número de dispositivos ligados ao seu hub IoT|Nenhum|
|Total de dispositivos (pré-visualização)|totalDeviceCount|de palavras|Média|Número de dispositivos registados no seu hub IoT|Nenhum|
|Dispositivos conectados (pré-visualização)|connectedDeviceCount|de palavras|Média|Número de dispositivos ligados ao seu hub IoT|Nenhum|

Para *dispositivos totais (precotados)* e *dispositivos ligados (preciectados)*, apenas é válida a agregação total (soma). Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

Para *dispositivos totais (pré-visualização)* e *dispositivos Ligados (pré-visualização),* apenas agregações mínimas, máximas e médias são válidas. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

*Os dispositivos conectados (pré-visualização)* e *os dispositivos totais (pré-visualização)* não são exportáveis através de definições de diagnóstico.

### <a name="device-telemetry-metrics"></a>Métricas de telemetria do dispositivo

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Número de erros de estrangulamento|d2c.telemetria.ingress.sendThrottle|de palavras|Total|Número de erros de estrangulamento devido aos aceleradores de produção do dispositivo|Nenhum|
|Mensagem de telemetria envia tentativas|d2c.telemetria.ingress.allProtocol|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem tentadas de ser enviadas para o seu hub IoT|Nenhum|
|Mensagens de telemetria enviadas|d2c.telemetria.ingress.success|de palavras|Total|Número de mensagens de telemetria dispositivo-nuvem enviadas com sucesso para o seu hub IoT|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="device-to-cloud-twin-operations-metrics"></a>Dispositivo para cloud twin operações métricas

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Leituras gémeas falhadas dos dispositivos|d2c.twin.read.failure|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo falhados.|Nenhum|
|Falhas em duas atualizações a partir de dispositivos|d2c.twin.update.failure|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo.|Nenhum|
|Tamanho de resposta de leituras gémeas de dispositivos|d2c.twin.read.size|Bytes|Média|O número de todas as leituras gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|Tamanho das atualizações gémeas dos dispositivos|d2c.twin.update.size|Bytes|Média|O tamanho total de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|
|Leituras gémeas bem sucedidas de dispositivos|d2c.twin.read.success|de palavras|Total|A contagem de todas as leituras gémeas iniciadas pelo dispositivo.|Nenhum|
|Atualizações gémeas bem sucedidas dos dispositivos|d2c.twin.update.success|de palavras|Total|A contagem de todas as atualizações gémeas iniciadas pelo dispositivo com sucesso.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="event-grid-metrics"></a>Métricas da grelha de eventos

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Entregas de Grelha de Eventos (pré-visualização)|EventGridDeliveries|de palavras|Total|O número de eventos IoT Hub publicados para a Event Grid. Utilize a dimensão Resultado para o número de pedidos bem sucedidos e falhados. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (.|Resultado:<br/>EventType<br>*Para obter mais informações, consulte [as dimensões métricas.](#metric-dimensions)*|
|Latência da grelha de eventos (pré-visualização)|EventGridLatency|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado para quando o evento foi publicado para a Event Grid. Este número é uma média entre todos os tipos de eventos. Utilize a dimensão EventType para ver a latência de um tipo específico de evento.|EventType<br>*Para obter mais informações, consulte [as dimensões métricas.](#metric-dimensions)*|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="jobs-metrics"></a>Métricas de emprego

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Trabalhos concluídos|empregos.concluídos|de palavras|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|Chamadas falhadas para listar empregos|empregos.listJobs.fracasso|de palavras|Total|A contagem de todas as chamadas falhadas para listar empregos.|Nenhum|
|Criações falhadas de trabalhos de invocação de métodos|jobs.createDirectMethodJob.fail|de palavras|Total|A contagem de todos os trabalhos falhados de invocação de métodos diretos.|Nenhum|
|Criações falhadas de trabalhos de atualização dupla|jobs.createTwinUpdateJob.failure|de palavras|Total|A contagem de todos os trabalhos falhados de atualização dupla.|Nenhum|
|Cancelamentos de empregos falhados|jobs.cancelJob.failure|de palavras|Total|A contagem de todas as chamadas falhadas para cancelar um emprego.|Nenhum|
|Consultas de trabalho falhadas|jobs.consultaJobs.falha|de palavras|Total|A contagem de todas as chamadas falhadas para consultas.|Nenhum|
|Empregos falhados|empregos.falhou|de palavras|Total|A contagem de todos os trabalhos falhados.|Nenhum|
|Chamadas bem-sucedidas para listar empregos|jobs.listJobs.sucesso|de palavras|Total|A contagem de todas as chamadas bem sucedidas para listar empregos.|Nenhum|
|Criações bem sucedidas de trabalhos de invocação de métodos|jobs.createDirectMethodJob.success|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de invocação de métodos diretos.|Nenhum|
|Criações bem sucedidas de trabalhos de atualização dupla|jobs.createTwinUpdateJob.<br>exito|de palavras|Total|A contagem de todos os trabalhos bem sucedidos de criação de trabalhos de atualização dupla.|Nenhum|
|Cancelamentos de emprego bem-sucedidos|jobs.cancelJob.sucesso|de palavras|Total|A contagem de todas as chamadas bem sucedidas para cancelar um emprego.|Nenhum|
|Consultas de trabalho bem sucedidas|jobs.queryJobs.sucesso|de palavras|Total|A contagem de todas as chamadas bem sucedidas para trabalhos de consulta.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="routing-metrics"></a>Métricas de encaminhamento

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
| Tentativas de entrega de encaminhamento (pré-visualização) |RoteamentoDelivas | de palavras | Total |Esta é a métrica de entrega de encaminhamento. Utilize as dimensões para identificar o estado de entrega de um ponto final específico ou para uma fonte de encaminhamento específica.| Resultado:<br>RoutingSource,<br>EndpointType,<br>FalhaReasonCategoria,<br>Nome de ponto final<br>*Para obter mais informações, consulte [as dimensões métricas.](#metric-dimensions)* |
| Tamanho dos dados de entrega de encaminhamento em bytes (pré-visualização)|EncaminhamentoDataSizeInBytesDelivered| Bytes | Total |O número total de bytes encaminhados pelo IoT Hub para o ponto final personalizado e o ponto final incorporado. Utilize as dimensões para identificar o tamanho dos dados encaminhados para um ponto final específico ou para uma fonte de encaminhamento específica.| RoutingSource,<br>Tipo de ponto final<br>Nome de ponto final<br>*Para obter mais informações, consulte [as dimensões métricas.](#metric-dimensions)*|
| Encaminhamento de Latência (pré-visualização) |EncaminhamentoDeliveryLatency| Milissegundos | Média |Esta é a métrica de latência de entrega de encaminhamento. Utilize as dimensões para identificar a latência de um ponto final específico ou para uma fonte de encaminhamento específica.| RoutingSource,<br>EndpointType,<br>Nome de ponto final<br>*Para obter mais informações, consulte [as dimensões métricas.](#metric-dimensions)*|
|Encaminhamento: bolhas entregues ao armazenamento|d2c.endpoints.egress.storage.blobs|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou bolhas nos pontos finais de armazenamento.|Nenhum|
|Encaminhamento: dados entregues ao armazenamento|d2c.endpoints.egress.storage.bytes|Bytes|Total|A quantidade de dados (bytes) IoT Hub de encaminhamento entregue nos pontos finais de armazenamento.|Nenhum|
|Encaminhamento: latência da mensagem para o Centro de Eventos|d2c.endpoints.latncy.eventHubs|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens em pontos finais personalizados do tipo Event Hub. Isto não inclui as rotas de mensagens para o ponto final incorporado (eventos).|Nenhum|
|Encaminhamento: latência da mensagem para a fila do autocarro de serviço|d2c.endpoints.latncy.serviceBusQueues|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de fila de autocarros de serviço.|Nenhum|
|Encaminhamento: latência de mensagem para o tópico do autocarro de serviço|d2c.endpoints.latncy.serviceBusTopics|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de ônibus de serviço.|Nenhum|
|Encaminhamento: latência da mensagem para mensagens/eventos|d2c.endpoints.latncy.builtIn.events|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens no ponto final incorporado (mensagens/eventos) e a rota de recuo.|Nenhum|
|Encaminhamento: latência da mensagem para armazenamento|d2c.endpoints.latncy.storage|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagens para o IoT Hub e a entrada de mensagens num ponto final de armazenamento.|Nenhum|
|Encaminhamento: mensagens entregues no Centro de Eventos|d2c.endpoints.egress.eventHubs|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso a pontos finais personalizados do tipo Event Hub. Isto não inclui as rotas de mensagens para o ponto final incorporado (eventos).|Nenhum|
|Encaminhamento: mensagens entregues na Fila de Autocarros de Serviço|d2c.endpoints.egress.serviceBusQueues|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais da fila do Service Bus.|Nenhum|
|Encaminhamento: mensagens entregues no Service Bus Topic|d2c.endpoints.egress.serviceBusTopics|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais do tópico do Service Bus.|Nenhum|
|Encaminhamento: mensagens entregues para recuo|d2c.telemetria.egress.fallback|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens ao ponto final associado à rota de retorno.|Nenhum|
|Encaminhamento: mensagens entregues a mensagens/eventos|d2c.endpoints.egress.builtIn.events|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso ao ponto final incorporado (mensagens/eventos) e à rota de retorno.|Nenhum|
|Encaminhamento: mensagens entregues no armazenamento|d2c.endpoints.egress.storage|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub entregou mensagens com sucesso aos pontos finais de armazenamento.|Nenhum|
|Encaminhamento: mensagens de telemetria entregues|d2c.telemetria.egress.sucesso|de palavras|Total|O número de vezes que as mensagens foram entregues com sucesso em todos os pontos finais utilizando o encaminhamento IoT Hub. Se uma mensagem for encaminhada para vários pontos finais, este valor aumenta um para cada entrega bem sucedida. Se uma mensagem for entregue no mesmo ponto final várias vezes, este valor aumenta um para cada entrega bem sucedida.|Nenhum|
|Encaminhamento: mensagens de telemetria caídas |d2c.telemetria.egress.drop|de palavras|Total|O número de vezes que as mensagens foram deixadas pelo itinerário do IoT Hub devido a pontos sem saída. Este valor não conta as mensagens entregues para a rota de retorno, uma vez que as mensagens deixadas não são entregues lá.|Nenhum|
|Encaminhamento: mensagens de telemetria incompatíveis|d2c.telemetria.egress.inválido|de palavras|Total|O número de vezes que o encaminhamento do IoT Hub não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto final. Uma mensagem é incompatível com um ponto final quando o Iot Hub tenta entregar a mensagem a um ponto final e falha com um erro não transitório. As mensagens inválidas não são novamente experimentadas. Este valor não inclui retrações.|Nenhum|
|Encaminhamento: mensagens de telemetria órfãs |d2c.telemetria.egress.órfão|de palavras|Total|O número de vezes que as mensagens foram órfãs pelo encaminhamento do IoT Hub porque não correspondem a qualquer consulta de encaminhamento, quando a rota de retorno é desativada.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

### <a name="twin-query-metrics"></a>Métricas de consulta dupla

|Nome de exibição métrica|Metric|Unidade|Tipo de Agregação|Description|Dimensões|
|---|---|---|---|---|---|
|Consultas gémeas falhadas|twinQueries.falha|de palavras|Total|A contagem de todas as consultas gémeas falhadas.|Nenhum|
|Consultas gémeas bem sucedidas|twinQueries.sucesso|de palavras|Total|A contagem de todas as consultas gémeas bem sucedidas.|Nenhum|
|Tamanho do resultado de consultas gémeas|twinQueries.resultSize|Bytes|Média|O total do tamanho do resultado de todas as consultas gémeas bem sucedidas.|Nenhum|

Para métricas com valor **unitário** de **Conde,** apenas a agregação total (soma) é válida. Agregações mínimas, máximas e médias regressam sempre 1. Para obter mais informações, consulte [agregações suportadas.](#supported-aggregations)

## <a name="metric-dimensions"></a>Dimensões métricas

O Azure IoT Hub tem as seguintes dimensões associadas a algumas das suas métricas de encaminhamento e grelha de evento.

|Nome da dimensão | Description|
|---|---|
||
|**Nome de ponto final**| O nome do ponto final.|
|**Tipo de ponto final**|Um dos **seguintes: eventHubs**, **serviceBusQueues,** **cosmosDB,** **serviceBusTopics**. **builtin**, ou **blobStorage**.|
|**EventType**| Um dos seguintes tipos de eventos da Grelha de **Eventos: Microsoft.Devices.DeviceCreated**. **Microsoft.Devices.DeviceDeleted**, **Microsoft.DeviceConnected**, **Microsoft.DeviceD.DeviceDisconnected , Microsoft.DeviceDisconnected**, ou **Microsoft.DeviceTelemetry**. Para obter mais informações, consulte [os tipos de Eventos.](iot-hub-event-grid.md#event-types)|
|**FalhaReasonCategoria**| Um dos seguintes: **inválido,** **caído,** **órfão,** ou **nulo.**|
|**Resultado**| Ou **sucesso** ou **fracasso.**|
|**RoutingSource**| Mensagens de dispositivo<br>Eventos de Mudança Dupla<br>Eventos de ciclo de vida do dispositivo|

Para saber mais sobre as dimensões métricas, consulte [métricas multidimensionais.](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

## <a name="resource-logs"></a>Registos do recurso

Esta secção lista todos os tipos e esquemas de categoria de registo de recursos recolhidos para o Azure IoT Hub. O fornecedor de recursos e o tipo para todos os registos IoT Hub é [Microsoft.Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs).

**Tópicos nesta secção**

- [Referência de dados do Azure IoT Hub](#monitoring-azure-iot-hub-data-reference)
  - [Métricas](#metrics)
    - [Agregações apoiadas](#supported-aggregations)
    - [Métricas de comando de nuvem para dispositivo](#cloud-to-device-command-metrics)
    - [Nuvem para dispositivo métodos diretos métricas](#cloud-to-device-direct-methods-metrics)
    - [Nuvem para dispositivo métricas de operações gémeas](#cloud-to-device-twin-operations-metrics)
    - [Métricas de configurações](#configurations-metrics)
    - [Métricas diárias de quota](#daily-quota-metrics)
    - [Métricas do dispositivo](#device-metrics)
    - [Métricas de telemetria do dispositivo](#device-telemetry-metrics)
    - [Dispositivo para cloud twin operações métricas](#device-to-cloud-twin-operations-metrics)
    - [Métricas da grelha de eventos](#event-grid-metrics)
    - [Métricas de emprego](#jobs-metrics)
    - [Métricas de encaminhamento](#routing-metrics)
    - [Métricas de consulta dupla](#twin-query-metrics)
  - [Dimensões métricas](#metric-dimensions)
  - [Registos do recurso](#resource-logs)
    - [Ligações](#connections)
    - [Telemetria do dispositivo](#device-telemetry)
    - [Comandos da cloud para dispositivo](#cloud-to-device-commands)
    - [Operações de identidade do dispositivo](#device-identity-operations)
    - [Operações de carregamento de ficheiros](#file-upload-operations)
    - [Rotas](#routes)
    - [Operações gémeas dispositivo-a-nuvem](#device-to-cloud-twin-operations)
    - [Operações gémeas nuvem-para-dispositivo](#cloud-to-device-twin-operations)
    - [Consultas gémeas](#twin-queries)
    - [Operações de tarefas](#jobs-operations)
    - [Métodos Diretos](#direct-methods)
    - [Rastreio distribuído (pré-visualização)](#distributed-tracing-preview)
      - [Troncos IoT Hub D2C (dispositivo-nuvem)](#iot-hub-d2c-device-to-cloud-logs)
      - [Troncos de entrada do IoT Hub](#iot-hub-ingress-logs)
      - [Troncos de saída do IoT Hub](#iot-hub-egress-logs)
    - [Configurações](#configurations)
    - [Streams de dispositivos (pré-visualização)](#device-streams-preview)
  - [Tabelas de registos do monitor Azure](#azure-monitor-logs-tables)
  - [Ver Também](#see-also)

### <a name="connections"></a>Ligações

A categoria de ligações rastreia o dispositivo conectar e desligar eventos de um hub IoT, bem como erros. Esta categoria é útil para identificar tentativas de ligação não autorizadas e ou alertar quando perde a ligação aos dispositivos.

> [!NOTE]
> Para obter uma ligação fiável dos dispositivos, verifique [o batimento cardíaco do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo rastreia erros que ocorrem no hub IoT e estão relacionados com o gasoduto de telemetria. Esta categoria inclui erros que ocorrem ao enviar eventos de telemetria (como estrangulamento) e receber eventos de telemetria (como leitor não autorizado). Esta categoria não pode apanhar erros causados pelo código em funcionamento no próprio dispositivo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Comandos da cloud para dispositivo

A categoria de comandos cloud-to-device rastreia erros que ocorrem no hub IoT e estão relacionados com o pipeline de mensagens nuvem-para-dispositivo. Esta categoria inclui erros que ocorrem a partir de:

* Envio de mensagens em nuvem para dispositivo (como erros não autorizados de remetente),
* Receber mensagens nuvem-dispositivo (como a contagem de entregas excedeu erros), e
* Receber feedback de mensagens nuvem-a-dispositivo (como erros expirados).

Esta categoria não apanha erros quando a mensagem nuvem-a-dispositivo é entregue com sucesso, mas depois manuseada indevidamente pelo dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando tenta criar, atualizar ou apagar uma entrada no registo de identidade do seu hub IoT. Rastrear esta categoria é útil para cenários de provisionamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operações de carregamento de ficheiros

A categoria de upload de ficheiros rastreia os erros que ocorrem no hub IoT e estão relacionados com a funcionalidade de upload de ficheiros. Esta categoria inclui:

* Erros que ocorrem com o SAS URI, como quando expira antes de um dispositivo notificar o centro de um upload concluído.

* Uploads falhados reportados pelo dispositivo.

* Erros que ocorrem quando um ficheiro não é encontrado no armazenamento durante a criação de mensagens de notificação IoT Hub.

Esta categoria não pode apanhar erros que ocorrem diretamente enquanto o dispositivo está a carregar um ficheiro para armazenamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Rotas

A categoria [de encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md) rastreia erros que ocorrem durante a avaliação da rota da mensagem e saúde do ponto final como percebido pelo IoT Hub. Esta categoria inclui eventos como:

* Uma regra avalia a "indefinida",
* IoT Hub marca um ponto final como morto, ou
* Quaisquer erros recebidos de um ponto final.

Esta categoria não inclui erros específicos sobre as próprias mensagens (como erros de estrangulamento do dispositivo), que são relatados na categoria "telemetria do dispositivo".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Aqui estão mais detalhes sobre os registos de recursos de encaminhamento:

* [Lista de códigos de erro de registo de recursos de encaminhamento](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista de registos de encaminhamento de registos de registos Desínios](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Operações gémeas dispositivo-a-nuvem

A categoria de operações gémeas dispositivo-a-nuvem rastreia eventos iniciados pelo dispositivo em gémeos dispositivos. Estas operações podem incluir obter twin, atualizar propriedades reportadas e subscrever as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Operações gémeas nuvem-para-dispositivo

A categoria de operações gémeas nuvem-a-dispositivo rastreia eventos iniciados pelo serviço em gémeos de dispositivos. Estas operações podem incluir obter etiquetas gémeas, atualizar ou substituir, e atualizar ou substituir as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Consultas gémeas

A categoria de consultas gémeas relata pedidos de consulta para gémeos de dispositivo que são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operações de tarefas

A categoria de operações de emprego reporta pedidos de emprego para atualizar gémeos de dispositivos ou invocar métodos diretos em vários dispositivos. Estes pedidos são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Métodos Diretos

A categoria de métodos diretos acompanha as interações de resposta de pedido enviadas a dispositivos individuais. Estes pedidos são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Rastreio distribuído (pré-visualização)

A categoria de rastreio distribuído rastreia os IDs de correlação para mensagens que transportam o cabeçalho de contexto de traços. Para ativar totalmente estes registos, o código do lado do cliente deve ser atualizado seguindo [as aplicações IoT de ponta a ponta com o rastreio distribuído do IoT Hub (pré-visualização)](iot-hub-distributed-tracing.md).

Note que `correlationId` está em conformidade com a proposta [W3C Trace Context,](https://github.com/w3c/trace-context) onde contém `trace-id` um bem como um `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Troncos IoT Hub D2C (dispositivo-nuvem)

O IoT Hub regista este registo quando uma mensagem que contém propriedades de vestígios válidos chega ao IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Aqui, `durationMs` não é calculado como o relógio do IoT Hub pode não estar sincronizado com o relógio do dispositivo, e assim um cálculo de duração pode ser enganador. Recomendamos escrever lógica usando os tempos na `properties` secção para capturar picos na latência dispositivo-nuvem.

| Propriedade | Tipo | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **mensagemSize** | Número inteiro | O tamanho da mensagem dispositivo-a-nuvem em bytes |
| **deviceId** | Cadeia de caracteres alfanuméricos ASCII de 7 bits | A identidade do dispositivo |
| **chamadorLocalTimeUtc** | Calendário UTC | O tempo de criação da mensagem como relatado pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Calendário UTC | A hora da chegada da mensagem à porta de entrada do IoT Hub, conforme relatado pelo relógio lateral de serviço IoT Hub |

#### <a name="iot-hub-ingress-logs"></a>Troncos de entrada do IoT Hub

O IoT Hub grava este registo quando a mensagem que contém propriedades de vestígios válidos escreve para o Centro de Eventos interno ou incorporado.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Verdadeiro ou falso, indica se o encaminhamento de mensagens está ou não ativado no Hub IoT |
| **parentSpanId** | String | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem principal, que seria o traço de mensagem D2C neste caso |

#### <a name="iot-hub-egress-logs"></a>Troncos de saída do IoT Hub

IoT Hub grava este registo quando [o encaminhamento](iot-hub-devguide-messages-d2c.md) está ativado e a mensagem é escrita para um [ponto final](iot-hub-devguide-endpoints.md). Se o encaminhamento não estiver ativado, o IoT Hub não grava este registo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` secção, este registo contém informações adicionais sobre a entrada de mensagens.

| Propriedade | Tipo | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | O nome do ponto final de encaminhamento |
| **endpointType** | String | O tipo de ponto final de encaminhamento |
| **parentSpanId** | String | O [span-id](https://w3c.github.io/trace-context/#parent-id) da mensagem dos pais, que seria o traço de mensagem de entrada IoT Hub neste caso |

### <a name="configurations"></a>Configurações

Os registos de configuração do IoT Hub rastreiam eventos e erros para o conjunto de funções de gestão automática de dispositivos.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Streams de dispositivos (pré-visualização)

A categoria de streams de dispositivos rastreia as interações de resposta de pedido enviadas para dispositivos individuais.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabelas de registos do monitor Azure
<!-- REQUIRED. Please keep heading in this order -->

Esta secção refere-se a todas as tabelas Azure Monitor Logs Kusto relevantes para o Azure IoT Hub e disponíveis para consulta por Log Analytics. Para obter uma lista destas tabelas e links para mais informações para o tipo de recurso IoT Hub, consulte o [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) na referência da tabela Azure Monitor Logs.

Para obter uma referência de todos os registos do Monitor Azure / Tabelas de Azure Monitor Analytics, consulte a referência da [tabela de registo do monitor Azure](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Consulte também

* Consulte [o Monitor Azure IoT Hub](monitor-iot-hub.md) para obter uma descrição da monitorização do Azure IoT Hub.
* Consulte [os recursos de Monitor Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.
