---
title: Usar métricas para monitorar o Hub IoT do Azure | Microsoft Docs
description: Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral de seus hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: f0bcf12a43a4732b371dd2d64c0b174a0087bea9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098945"
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
|D2C<br>. telemetria<br>entrada.<br>próprio protocolo|Tentativas de envio de mensagem de telemetria|Count|Total|Número de mensagens de telemetria do dispositivo para a nuvem que tentaram ser enviadas para o Hub IoT|Nenhuma dimensão|
|D2C<br>. telemetria<br>. entrada<br>. êxito|Mensagens de telemetria enviadas|Count|Total|Número de mensagens de telemetria do dispositivo para a nuvem enviadas com êxito para o Hub IoT|Nenhuma dimensão|
|C2D<br>. comandos<br>. saída<br>. concluído<br>. êxito|Comandos concluídos|Count|Total|Número de comandos da nuvem para o dispositivo concluídos com êxito pelo dispositivo|Nenhuma dimensão|
|C2D<br>. comandos<br>. saída<br>. abandono<br>. êxito|Comandos abandonados|Count|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|Nenhuma dimensão|
|C2D<br>. comandos<br>. saída<br>. rejeitar<br>. êxito|Comandos rejeitados|Count|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|Nenhuma dimensão|
|dispositivos<br>.totalDevices|Total de dispositivos (preterido)|Count|Total|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|dispositivos<br>.connectedDevices<br>. MyProtocol|Dispositivos conectados (preterido) |Count|Total|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|D2C<br>. telemetria<br>. saída<br>. êxito|Roteamento: mensagens de telemetria entregues|Count|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem for roteada para vários pontos de extremidade, esse valor aumentará em um para cada entrega bem-sucedida. Se uma mensagem for entregue ao mesmo ponto de extremidade várias vezes, esse valor aumentará em um para cada entrega bem-sucedida.|Nenhuma dimensão|
|D2C<br>. telemetria<br>. saída<br>. Descartado|Roteamento: mensagens de telemetria eliminadas |Count|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido a pontos de extremidade inativos. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens ignoradas não são entregues lá.|Nenhuma dimensão|
|D2C<br>. telemetria<br>. saída<br>. órfãos|Roteamento: mensagens de telemetria órfãs |Count|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma regra de roteamento (incluindo a norma de fallback). |Nenhuma dimensão|
|D2C<br>. telemetria<br>. saída<br>. inválido|Roteamento: mensagens de telemetria incompatíveis|Count|Total|O número de vezes que o roteamento do Hub IoT falhou ao entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui repetições.|Nenhuma dimensão|
|D2C<br>. telemetria<br>. saída<br>. fallback|Roteamento: mensagens entregues ao fallback|Count|Total|O número de vezes que o roteamento do Hub IoT fornece mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>.eventHubs|Roteamento: mensagens entregues ao Hub de eventos|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade do hub de eventos.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. latência<br>.eventHubs|Roteamento: latência de mensagem para o Hub de eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem em um ponto de extremidade do hub de eventos.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>.serviceBusQueues|Roteamento: mensagens entregues à fila do barramento de serviço|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens aos pontos de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. latência<br>.serviceBusQueues|Roteamento: latência de mensagem para a fila do barramento de serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do barramento de serviço.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>.serviceBusTopics|Roteamento: mensagens entregues ao tópico do barramento de serviço|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. latência<br>.serviceBusTopics|Roteamento: latência de mensagem para o tópico do barramento de serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>.builtIn<br>. eventos|Roteamento: mensagens entregues a mensagens/eventos|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. latência<br>.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>. armazenamento|Roteamento: mensagens entregues ao armazenamento|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. latência<br>. armazenamento|Roteamento: latência de mensagem para armazenamento|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem para o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>. armazenamento<br>. bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregues aos pontos de extremidade de armazenamento.|Nenhuma dimensão|
|D2C<br>. pontos de extremidade<br>. saída<br>. armazenamento<br>. BLOBs|Roteamento: BLOBs entregues ao armazenamento|Count|Total|O número de vezes que o roteamento do Hub IoT forneceu BLOBs para pontos de extremidade de armazenamento.|Nenhuma dimensão|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Count|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (. Para ver a origem das solicitações, use a dimensão EventType.|Resultado, EventType|
|EventGridLatency|Latência da grade de eventos (versão prévia)|Milissegundos|Average|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|D2C<br>.twin<br>. ler<br>. êxito|Leituras de entrelaçamento bem-sucedidas de dispositivos|Count|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|D2C<br>.twin<br>. ler<br>. falha|Leituras de entrelaçamento com falha de dispositivos|Count|Total|A contagem de todas as leituras de entrelaças iniciadas pelo dispositivo com falha.|Nenhuma dimensão|
|D2C<br>.twin<br>. ler<br>. tamanho|Tamanho da resposta de leituras de entrelaçamento de dispositivos|Bytes|Average|A média, o mínimo e o máximo de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma dimensão|
|D2C<br>.twin<br>. atualização<br>. êxito|Atualizações de atualização com êxito de dispositivos|Count|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|D2C<br>.twin<br>. atualização<br>. falha|Atualizações de atualização de falha de dispositivos|Count|Total|A contagem de todas as atualizações de atualização do dispositivo iniciadas com falha.|Nenhuma dimensão|
|D2C<br>.twin<br>. atualização<br>. tamanho|Tamanho de atualizações de atualização de papel dos dispositivos|Bytes|Average|O tamanho médio, mínimo e máximo de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma dimensão|
|C2D<br>. métodos<br>. êxito|Invocações de método diretos bem-sucedidas|Count|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma dimensão|
|C2D<br>. métodos<br>. falha|Invocações de método direto com falha|Count|Total|A contagem de todas as chamadas de método diretas com falha.|Nenhuma dimensão|
|C2D<br>. métodos<br>.requestSize|Tamanho da solicitação de invocações de método direto|Bytes|Average|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhuma dimensão|
|C2D<br>. métodos<br>.responseSize|Tamanho da resposta de invocações de método diretas|Bytes|Average|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhuma dimensão|
|C2D<br>.twin<br>. ler<br>. êxito|Leituras de cópia com êxito do back-end|Count|Total|A contagem de todas as leituras de cópia de bits iniciadas de back-end bem-sucedidas. Essa contagem não inclui leituras de entrelaçamento iniciadas a partir de consultas de entrelaçamento.|Nenhuma dimensão|
|C2D<br>.twin<br>. ler<br>. falha|Leituras de cópia com falha do back-end|Count|Total|A contagem de todas as leituras de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|C2D<br>.twin<br>. ler<br>. tamanho|Tamanho da resposta de leituras de cópia do back-end|Bytes|Average|A média, mín e máx de todas as leituras de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|C2D<br>.twin<br>. atualização<br>. êxito|Atualizações de cópia bem-sucedida do back-end|Count|Total|A contagem de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|C2D<br>.twin<br>. atualização<br>. falha|Atualizações de cópia com falha do back-end|Count|Total|A contagem de todas as atualizações de cópia de bits iniciadas pelo back-end com falha.|Nenhuma dimensão|
|C2D<br>.twin<br>. atualização<br>. tamanho|Tamanho das atualizações de cópia do back-end|Bytes|Average|O tamanho médio, mínimo e máximo de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma dimensão|
|twinQueries<br>. êxito|Consultas de entrelaçamento bem-sucedidas|Count|Total|A contagem de todas as consultas de myup com êxito.|Nenhuma dimensão|
|twinQueries<br>. falha|Consultas de entrelaçamento com falha|Count|Total|A contagem de todas as consultas de entrelaçamento com falha.|Nenhuma dimensão|
|twinQueries<br>.resultSize|Tamanho do resultado de consultas de entrelaçamento|Bytes|Average|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma dimensão|
|tarefas<br>.createTwinUpdateJob<br>. êxito|Criações bem-sucedidas de trabalhos de atualização de entrelaçamento|Count|Total|A contagem de todas as criações de trabalhos de atualização de entrelaçamento com êxito.|Nenhuma dimensão|
|tarefas<br>.createTwinUpdateJob<br>. falha|Criações com falha de trabalhos de atualização de entrelaçamento|Count|Total|A contagem de todas as falhas na criação de trabalhos de atualização de entrelaçamento.|Nenhuma dimensão|
|tarefas<br>.createDirectMethodJob<br>. êxito|Criações de trabalhos de invocação de método com êxito|Count|Total|A contagem de toda a criação bem-sucedida de trabalhos de invocação de método direto.|Nenhuma dimensão|
|tarefas<br>.createDirectMethodJob<br>. falha|Criações com falha de trabalhos de invocação de método|Count|Total|A contagem de todas as falhas na criação de trabalhos de invocação de método direto.|Nenhuma dimensão|
|tarefas<br>.listJobs<br>. êxito|Chamadas com êxito para listar trabalhos|Count|Total|A contagem de todas as chamadas bem-sucedidas para listar trabalhos.|Nenhuma dimensão|
|tarefas<br>.listJobs<br>. falha|Chamadas com falha para listar trabalhos|Count|Total|A contagem de todas as chamadas com falha para listar trabalhos.|Nenhuma dimensão|
|tarefas<br>.cancelJob<br>. êxito|Cancelamentos de trabalho bem-sucedidos|Count|Total|A contagem de todas as chamadas bem-sucedidas para cancelar um trabalho.|Nenhuma dimensão|
|tarefas<br>.cancelJob<br>. falha|Cancelamentos de trabalho com falha|Count|Total|A contagem de todas as chamadas com falha para cancelar um trabalho.|Nenhuma dimensão|
|tarefas<br>.queryJobs<br>. êxito|Consultas de trabalho com êxito|Count|Total|A contagem de todas as chamadas bem-sucedidas para os trabalhos de consulta.|Nenhuma dimensão|
|tarefas<br>.queryJobs<br>. falha|Consultas de trabalho com falha|Count|Total|A contagem de todas as chamadas com falha para trabalhos de consulta.|Nenhuma dimensão|
|tarefas<br>. concluído|Trabalhos concluídos|Count|Total|A contagem de todos os trabalhos concluídos.|Nenhuma dimensão|
|tarefas<br>. com falha|Trabalhos com falha|Count|Total|A contagem de todos os trabalhos com falha.|Nenhuma dimensão|
|D2C<br>. telemetria<br>. entrada<br>.sendThrottle|Número de erros de limitação|Count|Total|Número de erros de limitação devido a aceleradores de taxa de transferência do dispositivo|Nenhuma dimensão|
|dailyMessage<br>QuotaUsed|Número total de mensagens usadas|Count|Average|Número total de mensagens usadas hoje. Esse é um valor cumulativo que é redefinido para zero às 00:00 UTC todos os dias.|Nenhuma dimensão|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos de e para todos os dispositivos conectados ao IotHub|Nenhuma dimensão|
|totalDeviceCount|Total de dispositivos (visualização)|Count|Average|Número de dispositivos registrados no Hub IoT|Nenhuma dimensão|
|Connected<br>DeviceCount|Dispositivos conectados (visualização)|Count|Average|Número de dispositivos conectados ao seu hub IoT|Nenhuma dimensão|
|Figura|Métricas de configuração|Count|Total|Métricas para operações de configuração|Nenhuma dimensão|

## <a name="next-steps"></a>Passos seguintes

Agora que você já viu uma visão geral das métricas do Hub IoT, siga este link para saber mais sobre como gerenciar o Hub IoT do Azure:

* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)

* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
