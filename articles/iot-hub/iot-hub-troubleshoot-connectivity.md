---
title: Monitor e desconexão de problemas com Hub Azure IoT
description: Aprenda a monitorizar e resolver erros comuns com conectividade do dispositivo para o Hub Azure IoT
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759604"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitor, diagnóstico e desconexões de problemas com hub Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de resolver porque há muitos pontos possíveis de falha. Lógica de aplicação, redes físicas, protocolos, hardware, IoT Hub e outros serviços na nuvem podem causar problemas. A capacidade de detetar e identificar a origem de um problema é fundamental. No entanto, uma solução IoT em escala pode ter milhares de dispositivos, por isso não é prático verificar manualmente os dispositivos individuais. Para ajudá-lo a detetar, diagnosticar e resolver problemas em escala, utilize as capacidades de monitorização que o IoT Hub fornece através do Monitor Azure. Estas capacidades estão limitadas ao que o IoT Hub pode observar, pelo que recomendamos também que siga as melhores práticas de monitorização dos seus dispositivos e outros serviços Azure.

## <a name="get-alerts-and-error-logs"></a>Obtenha alertas e registos de erros

Utilize o Monitor Azure para obter alertas e escrever registos quando os dispositivos se desligarem.

### <a name="turn-on-diagnostic-logs"></a>Ligue os registos de diagnóstico

Para registar eventos e erros de ligação do dispositivo, ligue os diagnósticos para o IoT Hub. Recomendamos que se ligar estes registos o mais cedo possível, porque se os registos de diagnóstico não estiverem ativados, quando ocorrerem desconexões do dispositivo, não terá qualquer informação para resolver o problema.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub ioT.

3. Selecione **definições de Diagnóstico**.

4. Selecione **Ligar os diagnósticos**.

5. Ativar os registos de **Ligações** para recolher.

6. Para uma análise mais fácil, ligue **Enviar para Registar Analytics** [(ver preços).](https://azure.microsoft.com/pricing/details/log-analytics/) Veja o exemplo em [resolver erros](#resolve-connectivity-errors)de conectividade .

   ![Definições recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para saber mais, consulte [Monitorize a saúde do Hub Azure IoT e diagnostice rapidamente problemas](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurar alertas para a desconexão do dispositivo à escala

Para obter alertas quando os dispositivos se desligarem, configure os alertas na métrica dos **dispositivos Conectados (pré-visualização).**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub ioT.

3. Selecione **Alertas**.

4. Selecione **Nova regra de alerta**.

5. **Selecione Adicionar condição**e, em seguida, selecione "Dispositivos conectados (pré-visualização)".

6. Instale o limiar e alerte seguindo as instruções.

Para saber mais, veja [quais são os alertas no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>Deteção de desconexões individuais do dispositivo

Para detetar desconexões *por dispositivo,* como quando precisa de saber que uma fábrica acabou de ficar offline, [configurar eventos de desconexão do dispositivo com a Rede de Eventos](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Resolver erros de conectividade

Quando liga os registos de diagnóstico e alertas para dispositivos conectados, recebe alertas quando ocorrem erros. Esta secção descreve como procurar problemas comuns quando recebe um alerta. Os passos abaixo assumem que configuraste os registos do Monitor Azure para os seus registos de diagnóstico.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue pelo seu hub ioT.

1. Selecionar **Registos**.

1. Para isolar os registos de erro de conectividade para o IoT Hub, introduza a seguinte consulta e, em seguida, selecione **Executar:**

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, `OperationName` `ResultType` procure , (código de erro) e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de registo de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Siga os guias de resolução de problemas para os erros mais comuns:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenéricoTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Tentei os passos, mas não funcionaram.

Se os passos anteriores não ajudaram, tente:

* Se tiver acesso aos dispositivos problemáticos, física ou remotamente (como o SSH), siga o [guia de resolução de problemas](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) do lado do dispositivo para continuar a resolução de problemas.

* Verifique se os seus dispositivos estão **ativados** no portal Azure > o seu hub IoT > dispositivos IoT.

* Se o seu dispositivo utilizar o protocolo MQTT, verifique se a porta 8883 está aberta. Para mais informações, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Obtenha ajuda do [fórum Azure IoT Hub,](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub) [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)ou [suporte Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação para todos, deixe um comentário na secção de feedback abaixo se este guia não o ajudar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a resolução de problemas transitórios, consulte o tratamento transitório de [falhas.](/azure/architecture/best-practices/transient-faults)

* Para saber mais sobre o Azure IoT SDK e gerir as repetições, consulte como gerir a conectividade e mensagens fiáveis utilizando o [dispositivo Azure IoT Hub SDKs](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
