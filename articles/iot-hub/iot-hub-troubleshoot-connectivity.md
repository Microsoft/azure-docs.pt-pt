---
title: Desconectações de monitorização e resolução de problemas com O Hub IoT do Azure
description: Aprenda a monitorizar e a resolver erros comuns com a conectividade do dispositivo para o Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: f7073fbf39344fe39e179d55a5a8f395a6ba6240
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357427"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitor, diagnóstico e resolução de problemas desliga-se com O Hub IoT do Azure

Problemas de conectividade para dispositivos IoT podem ser difíceis de resolver problemas porque existem muitos pontos possíveis de falha. A lógica de aplicação, redes físicas, protocolos, hardware, IoT Hub e outros serviços na nuvem podem causar problemas. A capacidade de detetar e identificar a origem de um problema é fundamental. No entanto, uma solução IoT em escala poderia ter milhares de dispositivos, por isso não é prático verificar os dispositivos individuais manualmente. O IoT Hub integra-se com dois serviços Azure para o ajudar:

* **Monitor Azure** Para ajudá-lo a detetar, diagnosticar e resolver problemas em escala, utilize as capacidades de monitorização que o IoT Hub fornece através do Azure Monitor. Isto inclui a configuração de alertas para desencadear notificações e ações quando ocorrem desconexões e configurar registos que pode utilizar para descobrir as condições que causaram desconexões.

* **Grelha de eventos de Azure** Para infraestruturas críticas e desconexões por dispositivo, utilize a Grelha de Eventos Azure para subscrever os eventos de ligação e desconexão do dispositivo emitidos pelo IoT Hub.

Em ambos os casos, estas capacidades estão limitadas ao que o IoT Hub pode observar, pelo que também recomendamos que siga as melhores práticas de monitorização para os seus dispositivos e outros serviços Azure.

## <a name="event-grid-vs-azure-monitor"></a>Grelha de Eventos vs. Monitor Azure

A Grade de Eventos fornece uma solução de monitorização por dispositivo de baixa latência que pode utilizar para rastrear ligações de dispositivos para dispositivos críticos e infraestruturas. O Azure Monitor fornece uma métrica, *dispositivos conectados,* que pode utilizar para monitorizar o número de dispositivos ligados ao seu Hub IoT e desencadear um alerta quando esse número cair abaixo de um limiar estático.

Considere o seguinte ao decidir se deve utilizar a Grade de Eventos ou o Monitor Azure para um determinado cenário:

* Latência de alerta: Os eventos de conexão IoT Hub são entregues muito mais rapidamente através da Grade de Eventos. Isto faz com que a Grade de Eventos seja uma melhor escolha para cenários onde a notificação rápida é desejável.

* Notificações por dispositivo: A Grade de Eventos fornece a capacidade de rastrear ligações e desconexões para dispositivos individuais. Isto faz com que a Grelha de Eventos seja uma escolha melhor para cenários onde é necessário monitorizar as ligações para dispositivos críticos.

* Configuração leve: Os alertas métricos do Azure Monitor proporcionam uma experiência de configuração leve que não requer a integração com outros serviços para entregar notificações através de Email, SMS, Voice e outras notificações.  Com a Grade de Eventos, você precisa se integrar com outros serviços Azure para entregar notificações. Ambos os serviços podem integrar-se com outros serviços para desencadear ações mais complexas.

Devido às suas capacidades de baixa latência, por dispositivo, para ambientes de produção, recomendamos vivamente a utilização da Grade de Eventos para monitorizar as ligações. Claro que a escolha não é exclusiva, pode utilizar alertas métricos Azure Monitor e Grade de Eventos. Independentemente da sua escolha para rastrear desconexões, provavelmente utilizará registos de recursos do Azure Monitor para ajudar a resolver problemas das razões das desconexões inesperadas do dispositivo. As seguintes secções discutem cada uma destas opções com mais detalhes.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Grelha de eventos: Dispositivo monitor conecta e desliga eventos

Para monitorizar os eventos de ligação e desconexão do dispositivo na produção, recomendamos a subscrição dos [eventos **DeviceConnected** e **DeviceDis conectados**](iot-hub-event-grid.md#event-types) na Grelha de Eventos para acionar alertas e monitorizar o estado de ligação do dispositivo. O Event Grid proporciona uma latência de eventos muito mais baixa do que o Azure Monitor, e pode monitorizar por dispositivo, em vez do número total de dispositivos conectados. Estes fatores fazem da Grade de Evento o método preferido para monitorizar dispositivos críticos e infraestruturas.

Quando utilizar a Grade de Eventos para monitorizar ou acionar alertas nas desconexões do dispositivo, certifique-se de que constrói de forma a filtrar as desconexões periódicas devido à renovação do sinal SAS em dispositivos que utilizam os SDKs Azure IoT. Para saber mais, consulte o [comportamento de desconexão do dispositivo MQTT com SDKs Azure IoT](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Explore os seguintes tópicos para saber mais sobre a monitorização de eventos de ligação do dispositivo com a Grade de Eventos:

* Para uma visão geral da utilização da Grade de Eventos com IoT Hub, consulte [reagem aos eventos do IoT Hub com a Grade de Eventos.](iot-hub-event-grid.md) Preste especial atenção às limitações para a secção [de eventos ligados ao dispositivo e desligados](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) do dispositivo.

* Para obter um tutorial sobre a encomenda de eventos de ligação do dispositivo, consulte [eventos de ligação do dispositivo da Ordem a partir do Azure IoT Hub utilizando Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Para obter um tutorial sobre o envio de notificações por e-mail, consulte [Enviar notificações por e-mail sobre eventos do Azure IoT Hub utilizando a Grelha de Eventos e aplicações lógicas](/azure/event-grid/publish-iot-hub-events-to-logic-apps) na documentação da Grade de Eventos.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Monitor Azure: Eventos de ligação de rotas para registos

O hub IoT emite continuamente registos de recursos para várias categorias de operações. No entanto, para recolher estes dados de registo, é necessário criar uma definição de diagnóstico para os encaminhar para um destino onde possam ser analisados ou arquivados. Um desses destinos é o Azure Monitor Logs através de um espaço de trabalho Log Analytics[(ver preços),](https://azure.microsoft.com/pricing/details/log-analytics/)onde pode analisar os dados usando consultas kusto.

A [categoria de registos de registos](monitor-iot-hub-reference.md#connections) de registos IoT Hub emite operações e erros relacionados com ligações ao dispositivo. A imagem que se segue mostra uma definição de diagnóstico para encaminhar estes registos para um espaço de trabalho log analytics:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Definição recomendada para enviar registos de conectividade para log analytics espaço de trabalho.":::

Recomendamos a criação de uma definição de diagnóstico o mais cedo possível depois de criar o seu hub IoT, porque, embora o IoT Hub emita sempre registos de recursos, não são recolhidos pelo Azure Monitor até que os encaminhe para um destino.

Para saber mais sobre os registos de encaminhamento para um destino, consulte [a Coleção e o encaminhamento.](monitor-iot-hub.md#collection-and-routing) Para obter instruções detalhadas para criar uma definição de diagnóstico, consulte o [tutorial de métricas e registos de utilização](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Monitor Azure: Confiúde os alertas métricos para desconexão do dispositivo à escala

Pode configurar alertas com base nas métricas da plataforma emitidas pelo IoT Hub. Com alertas métricos, pode notificar os indivíduos de que ocorreu uma condição de interesse e também desencadear ações que possam responder automaticamente a essa condição.

A métrica [*dos dispositivos conectados (pré-visualização)*](monitor-iot-hub-reference.md#device-metrics) indica-lhe quantos dispositivos estão ligados ao seu Hub IoT. Pode criar alertas para acionar se esta métrica cair abaixo de um valor limiar:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Alerte as definições lógicas para a métrica dos dispositivos conectados.":::

Pode utilizar regras de alerta métricas para monitorizar anomalias de desconexão do dispositivo à escala. Isto é, quando um número significativo de dispositivos se desliga inesperadamente. Quando tal ocorrência é detetada, pode olhar para os registos para ajudar a resolver o problema. Para monitorizar as desconexões e desconexões por dispositivos críticos; no entanto, deve utilizar a Grade de Eventos. O Event Grid também proporciona uma experiência mais em tempo real do que as métricas Azure.

Para saber mais sobre alertas com o IoT Hub, consulte [Alertas no Monitor IoT Hub.](monitor-iot-hub.md#alerts) Para obter uma 3-0 na criação de alertas no IoT Hub, consulte o [tutorial de métricas e registos de utilização.](tutorial-use-metrics-and-diags.md) Para obter uma visão geral mais detalhada dos alertas, consulte [a visão geral dos alertas no Microsoft Azure](../azure-monitor/platform/alerts-overview.md) na documentação do Azure Monitor.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Monitor Azure: Utilize registos para resolver erros de conectividade

Quando detetar desconexões de dispositivos, seja com alertas métricos do Azure Monitor ou com a Grade de Eventos, pode utilizar registos para ajudar a resolver problemas. Esta secção descreve como procurar problemas comuns em Registos monitores Azure. Os passos abaixo assumem que já criou uma [definição de diagnóstico](#azure-monitor-route-connection-events-to-logs) para enviar registos de Ligações IoT Hub para um espaço de trabalho do Log Analytics.

Depois de criar uma definição de diagnóstico para encaminhar os registos de recursos do IoT Hub para os Registos do Monitor Azure, siga estes passos para visualizar os registos no portal Azure.

1. Navegue até ao seu hub IoT no [portal Azure.](https://portal.azure.com)

1. Em **Monitorização** no painel esquerdo do seu hub IoT, Selecione **Logs**.

1. Para isolar os registos de erros de conectividade para o IoT Hub, insira a seguinte consulta no editor de consulta e, em seguida, selecione **Executar** :

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName` , `ResultType` (código de erro) e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de registo de erro](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Uma vez identificado o erro, siga os guias de resolução de problemas para obter ajuda com os erros mais comuns:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Comportamento de desconexão do dispositivo MQTT com SDKs Azure IoT

Os SDKs do dispositivo Azure IoT desligam-se do IoT Hub e depois reconectam-se quando renovam os tokens SAS sobre o protocolo MQTT (e MQTT sobre WebSockets). Nos registos, isto aparece como um dispositivo informativo desligar e ligar eventos às vezes acompanhados por eventos de erro.

Por padrão, o tempo de vida útil do símbolo é de 60 minutos para todos os SDKs; no entanto, pode ser alterado por desenvolvedores em alguns dos SDKs. A tabela seguinte resume o tempo de vida simbólico, a renovação simbólica e o comportamento de renovação simbólica para cada um dos SDKs:

| SDK | Tempo de vida simbólico | Renovação simbólica | Comportamento de renovação |
|-----|----------|---------------------|---------|
| .NET | 60 minutos, configurável | 85% do tempo de vida, configurável | A SDK liga e desliga no tempo de vida útil do token mais um período de graça de 10 minutos. Eventos informativos e erros gerados em registos. |
| Java | 60 minutos, configurável | 85% do tempo de vida, não configurável | A SDK liga e desliga no tempo de vida útil do token mais um período de graça de 10 minutos. Eventos informativos e erros gerados em registos. |
| Node.js | 60 minutos, configurável | configurável | A SDK liga e desliga na renovação simbólica. Apenas eventos informativos são gerados em registos. |
| Python | 60 minutos, não configurável | -- | O SDK liga e desliga no tempo de vida útil do símbolo. |

As imagens seguintes mostram o comportamento de renovação de fichas em Registos monitores Azure para diferentes SDKs. O prazo de vida simbólico e o limiar de renovação foram alterados em partir dos seus incumprimentos, como observado.

* .NET device SDK com uma duração de vida simbólica de 1200 segundos (20 minutos) e renovação definida para acontecer a 90% da vida útil. as desconexões acontecem a cada 30 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Erro de comportamento para renovação de fichas sobre MQTT em Registos monitores Azure com .NET SDK.":::

* Java SDK com uma esperança de vida simbólica de 300 segundos (5 minutos) e 85% da renovação de vida útil. As desconexões acontecem a cada 15 minutos:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Erro de comportamento para renovação de fichas sobre MQTT em Registos monitores Azure com Java SDK.":::

* Nó SDK com uma vida útil simbólica de 300 segundos (5 minutos) e renovação simbólica definida para acontecer em 3 minutos. As desconexões acontecem na renovação simbólica. Além disso, não existem erros, apenas são emitidos eventos informativos de ligação/desconexão:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Erro de comportamento para renovação de fichas sobre MQTT em Registos monitores Azure com Nó SDK.":::

A seguinte consulta foi usada para recolher os resultados. A consulta extrai o nome e a versão SDK do saco de propriedade; para saber mais, consulte a [versão SDK em registos IoT Hub](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Como desenvolvedor ou operador de soluções IoT, é necessário estar atento a este comportamento para interpretar eventos de ligação/desconexão e erros relacionados nos registos. Se pretender alterar o tempo de vida útil do símbolo ou o comportamento de renovação dos dispositivos, verifique se o dispositivo implementa uma configuração gémea do dispositivo ou um método do dispositivo que o torna possível.

Se estiver a monitorizar as ligações do dispositivo com o Event Hub, certifique-se de que constrói de forma a filtrar as desconexões periódicas devido à renovação do sinal SAS; por exemplo, ao não desencadear ações baseadas em desconexões, desde que o evento de desconexão seja seguido por um evento de ligação dentro de um determinado período de tempo.

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Tentei os passos, mas não funcionaram.

Se os passos anteriores não ajudaram, tente:

* Se tiver acesso aos dispositivos problemáticos, física ou remotamente (como o SSH), siga o [guia de resolução de problemas do lado](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) do dispositivo para continuar a resolução de problemas.

* Verifique se os seus dispositivos estão **ativados** no portal Azure > o seu hub IoT > dispositivos IoT.

* Se o seu dispositivo utilizar o protocolo MQTT, verifique se a porta 8883 está aberta. Para obter mais informações, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obtenha ajuda do [Microsoft Q&Uma página de perguntas para Azure IoT Hub](/answers/topics/azure-iot-hub.html), Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)ou [suporte Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na secção de feedback abaixo se este guia não o ajudar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a resolução de problemas transitórios, consulte [o tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre a Azure IoT SDK e gerir as retrações, consulte [Como gerir a conectividade e mensagens fiáveis utilizando SDKs de dispositivo Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).