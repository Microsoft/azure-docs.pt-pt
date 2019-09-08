---
title: Diagnosticar e solucionar problemas de desconexão com o Hub IoT do Azure
description: Aprenda a diagnosticar e solucionar erros comuns com conectividade de dispositivo para o Hub IoT do Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801422"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Detectar e solucionar problemas de desconexão com o Hub IoT do Azure

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos pontos de falha possíveis. A lógica do aplicativo no lado do dispositivo, as redes físicas, os protocolos, o hardware e o Hub IoT do Azure podem causar problemas. Este artigo fornece recomendações sobre como detectar e solucionar problemas de conectividade do dispositivo do lado da nuvem (em oposição ao lado do dispositivo).

## <a name="get-alerts-and-error-logs"></a>Obter alertas e logs de erros

Use Azure Monitor para obter alertas e gravar logs quando as conexões de dispositivo forem descartadas.

### <a name="turn-on-diagnostic-logs"></a>Ativar registos de diagnóstico

Para registrar eventos e erros de conexão do dispositivo, ative o diagnóstico para o Hub IoT.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue até o Hub IoT.

3. Selecione **configurações de diagnóstico**.

4. Selecione **ativar os diagnósticos**.

5. Habilite os logs de **conexões** a serem coletados.

6. Para uma análise mais fácil, ative **Enviar para log Analytics** ([consulte preços](https://azure.microsoft.com/pricing/details/log-analytics/)). Consulte o exemplo em [resolver erros de conectividade](#resolve-connectivity-errors).

   ![Definições recomendadas](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Para saber mais, consulte [monitorar a integridade do Hub IOT do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Configurar alertas para a métrica de contagem de _dispositivos conectados_

Para obter alertas quando dispositivos se desconectarem, configure alertas na métrica **dispositivos conectados (versão prévia)** .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue até o Hub IoT.

3. Selecione **alertas**.

4. Selecione **nova regra de alerta**.

5. Selecione **Adicionar condição**e, em seguida, selecione "dispositivos conectados (versão prévia)".

6. Conclua a configuração dos limites e das opções de alerta desejadas seguindo os prompts.

Para saber mais, confira [o que são alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Resolver erros de conectividade

Ao ativar os logs de diagnóstico e alertas para dispositivos conectados, você obtém alertas quando ocorrem erros. Esta seção descreve como resolver problemas comuns quando você recebe um alerta. As etapas a seguir pressupõem que você configurou os logs de Azure Monitor para seus logs de diagnóstico.

1. Acesse seu espaço de trabalho para **log Analytics** na portal do Azure.

2. Selecione **pesquisa de logs**.

3. Para isolar logs de erros de conectividade para o Hub IoT, insira a seguinte consulta e, em seguida, selecione **executar**:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Se houver resultados, procure, `OperationName` `ResultType` (código de erro) e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.

   ![Exemplo de log de erros](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Use esta tabela para entender e resolver erros comuns.

    | Erro | Causa raiz | Resolução |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | A conexão foi fechada pelo dispositivo, mas o Hub IoT não sabe por quê. As causas comuns incluem tempo limite de MQTT/AMQP e perda de conectividade com a Internet. | Verifique se o dispositivo pode se conectar ao Hub IoT [testando a conexão](tutorial-connectivity.md). Se a conexão estiver correta, mas o dispositivo se desconectar intermitentemente, certifique-se de implementar a lógica de dispositivo Keep Alive adequada para sua escolha de protocolo (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | O Hub IoT não pôde autenticar a conexão. | Verifique se a SAS ou outro token de segurança que você usa não expirou. Os [SDKs do Azure IOT](iot-hub-devguide-sdks.md) geram automaticamente tokens sem a necessidade de configuração especial. |
    | 409002 LinkCreationConflict | Um dispositivo tem mais de uma conexão. Quando uma nova solicitação de conexão chega a um dispositivo, o Hub IoT fecha o anterior com esse erro. | No caso mais comum, um dispositivo detecta uma desconexão e tenta restabelecer a conexão, mas o Hub IoT ainda considera o dispositivo conectado. O Hub IoT fecha a conexão anterior e registra esse erro. Esse erro geralmente aparece como um efeito colateral de um problema diferente e transitório, portanto, procure outros erros nos logs para solucionar os problemas. Caso contrário, certifique-se de emitir uma nova solicitação de conexão somente se a conexão cair. |
    | 500001 ServerError | O Hub IoT teve um problema do lado do servidor. Provavelmente, o problema é transitório. Embora a equipe do Hub IoT trabalhe difícil para manter [o SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), subconjuntos pequenos de nós do Hub IOT podem ocasionalmente apresentar falhas transitórias. Quando o dispositivo tentar se conectar a um nó que está tendo problemas, você receberá esse erro. | Para atenuar a falha transitória, emita uma nova tentativa do dispositivo. Para [gerenciar automaticamente as tentativas](iot-hub-reliability-features-in-sdks.md#connection-and-retry), certifique-se de usar a versão mais recente dos [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).<br><br>Para obter uma prática recomendada sobre o tratamento de falhas transitórias e novas tentativas, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).  <br><br>Se o problema persistir após novas tentativas, verifique [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) e o [status do Azure](https://azure.microsoft.com/status/history/) para ver se o Hub IOT tem um problema conhecido. Se não houver nenhum problema conhecido e o problema persistir, [entre em contato com o suporte](https://azure.microsoft.com/support/options/) para obter mais investigações. |
    | 500008 GenericTimeout | O Hub IoT não pôde concluir a solicitação de conexão antes de atingir o tempo limite. Como um ServerError de 500001, esse erro é provavelmente transitório. | Siga as etapas de solução de problemas para um ServerError de 500001 para a causa raiz e resolva esse erro.|

## <a name="other-steps-to-try"></a>Outras etapas a serem experimentadas

Se as etapas anteriores não ajudarem, você poderá tentar:

* Se você tiver acesso aos dispositivos problemáticos, de forma física ou remota (como SSH), siga o [Guia de solução de problemas no lado do dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) para continuar a solução de problemas.

* Verifique se os dispositivos estão **habilitados** no portal do Azure > seu hub IOT > dispositivos IOT.

* Obtenha ajuda do [Fórum do Hub IOT do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)ou [suporte do Azure](https://azure.microsoft.com/support/options/).

Para ajudar a melhorar a documentação de todos, deixe um comentário na seção de comentários abaixo se este guia não o ajudar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como resolver problemas transitórios, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

* Para saber mais sobre o SDK do Azure IoT e gerenciar novas tentativas, consulte [como gerenciar conectividade e mensagens confiáveis usando os SDKs do dispositivo do Hub IOT do Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
