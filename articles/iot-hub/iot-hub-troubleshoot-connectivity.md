---
title: Desconectações de monitorização e resolução de problemas com O Hub IoT do Azure
description: Aprenda a monitorizar e a resolver erros comuns com a conectividade do dispositivo para o Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b22573fc98d4647c2b05763d9b67c37f6f023f98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322725"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitor, diagnóstico e resolução de problemas desliga-se com O Hub IoT do Azure

Problemas de conectividade para dispositivos IoT podem ser difíceis de resolver problemas porque existem muitos pontos possíveis de falha. A lógica de aplicação, redes físicas, protocolos, hardware, IoT Hub e outros serviços na nuvem podem causar problemas. A capacidade de detetar e identificar a origem de um problema é fundamental. No entanto, uma solução IoT em escala poderia ter milhares de dispositivos, por isso não é prático verificar os dispositivos individuais manualmente. Para ajudá-lo a detetar, diagnosticar e resolver problemas em escala, utilize as capacidades de monitorização que o IoT Hub fornece através do Azure Monitor. Estas capacidades estão limitadas ao que o IoT Hub pode observar, pelo que também recomendamos que siga as melhores práticas de monitorização para os seus dispositivos e outros serviços Azure.

## <a name="get-alerts-and-error-logs"></a>Receba alertas e registos de erros

Utilize o Azure Monitor para obter alertas e escrever registos quando os dispositivos se desligarem.

### <a name="turn-on-diagnostic-logs"></a>Ligue registos de diagnóstico

Para registar eventos e erros de ligação do dispositivo, ligue os diagnósticos para o IoT Hub. Recomendamos ligar estes registos o mais cedo possível, porque se os registos de diagnóstico não estiverem ativados, quando ocorrerem desconexões do dispositivo, não terá qualquer informação para resolver o problema.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub IoT.

3. Selecione **definições de Diagnóstico**.

4. **Selecione Ligue os diagnósticos**.

5. Ativar os **registos de Ligações** a serem recolhidos.

6. Para uma análise mais fácil, ligue **enviar para registar análises** [(ver preços).](https://azure.microsoft.com/pricing/details/log-analytics/) Veja o exemplo sob [erros de conectividade resolver](#resolve-connectivity-errors).

   ![Configurações recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para saber mais, consulte [monitorar a saúde do Azure IoT Hub e diagnosticar rapidamente os problemas.](iot-hub-monitor-resource-health.md)

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurar alertas para desconexão do dispositivo à escala

Para obter alertas quando os dispositivos se desligam, configura alertas na métrica **dos dispositivos conectados (pré-visualização).**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub IoT.

3. Selecione **Alertas**.

4. Selecione **Nova regra de alerta**.

5. **Selecione Adicionar a condição**e, em seguida, selecione "Dispositivos ligados (pré-visualização)".

6. Configurar o limiar e alertar seguindo as instruções.

Para saber mais, veja [quais são os alertas no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>Deteção de desconexões individuais de dispositivos

Para detetar desconexões *por dispositivo,* como quando é necessário saber que uma fábrica acabou de ficar offline, [configurar eventos de desconexão do dispositivo com a Grade de Eventos](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Resolver erros de conectividade

Quando liga registos de diagnóstico e alertas para dispositivos conectados, recebe alertas quando ocorrem erros. Esta secção descreve como procurar questões comuns quando recebe um alerta. Os passos abaixo assumem que configuraste registos do Azure Monitor para os seus registos de diagnóstico.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue pelo seu hub IoT.

1. Selecionar **Registos**.

1. Para isolar os registos de erros de conectividade para o IoT Hub, introduza a seguinte consulta e, em seguida, selecione **Executar**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure `OperationName` , `ResultType` (código de erro) e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de registo de erro](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Siga os guias de resolução de problemas para os erros mais comuns:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Tentei os passos, mas não funcionaram.

Se os passos anteriores não ajudaram, tente:

* Se tiver acesso aos dispositivos problemáticos, física ou remotamente (como o SSH), siga o [guia de resolução de problemas do lado](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) do dispositivo para continuar a resolução de problemas.

* Verifique se os seus dispositivos estão **ativados** no portal Azure > o seu hub IoT > dispositivos IoT.

* Se o seu dispositivo utilizar o protocolo MQTT, verifique se a porta 8883 está aberta. Para obter mais informações, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obtenha ajuda do [Microsoft Q&Uma página de perguntas para Azure IoT Hub](https://docs.microsoft.com/answers/topics/azure-iot-hub.html), Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)ou [suporte Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na secção de feedback abaixo se este guia não o ajudar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a resolução de problemas transitórios, consulte [o tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre a Azure IoT SDK e gerir as retrações, consulte [Como gerir a conectividade e mensagens fiáveis utilizando SDKs de dispositivo Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
