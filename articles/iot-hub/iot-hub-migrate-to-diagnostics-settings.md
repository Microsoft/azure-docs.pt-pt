---
title: Migrar as operações do Azure IoT Hub monitorizando os registos de recursos do IoT Hub em Azure Monitor | Microsoft Docs
description: Como atualizar o Azure IoT Hub para utilizar o Azure Monitor em vez de monitorizar as operações para monitorizar o estado das operações no seu hub IoT em tempo real.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591844"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrar o seu Hub IoT da monitorização de operações para registos de recursos do Azure Monitor

Os clientes que utilizam [a monitorização](iot-hub-operations-monitoring.md) de operações para monitorizar o estado das operações no IoT Hub podem migrar esse fluxo de trabalho para registos de recursos do [Azure Monitor](../azure-monitor/essentials/platform-logs-overview.md), uma funcionalidade do Azure Monitor. Os registos de recursos fornecem informações de diagnóstico ao nível do recurso para muitos serviços Azure.

**A funcionalidade de monitorização de operações do IoT Hub está depreciada**, e foi removida do portal. Este artigo fornece medidas para mover as suas cargas de trabalho da monitorização de operações para registos de recursos do Azure Monitor. Para obter mais informações sobre a linha temporal de depreciação, consulte [monitorar as suas soluções Azure IoT com o Azure Monitor e a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Atualização IoT Hub

Para atualizar o seu Hub IoT no portal Azure, primeiro crie uma definição de diagnóstico e, em seguida, desligue a monitorização das operações.  

### <a name="create-a--diagnostic-setting"></a>Criar uma definição de diagnóstico

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

1. No painel esquerdo, em **Monitorização,** selecione **Definições de Diagnóstico**. Em seguida, **selecione Adicionar a definição de diagnóstico**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Screenshot que realça as definições de Diagnóstico na secção de Monitorização.":::

1. No painel **de definição de diagnóstico,** dê um nome à definição de diagnóstico.

1. Em **detalhes de categoria,** selecione as categorias para as operações que deseja monitorizar. Para obter mais informações sobre as categorias de operações disponíveis com o IoT Hub, consulte [os registos de recursos.](monitor-iot-hub-reference.md#resource-logs)

1. Em **Detalhes do Destino,** escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação destes destinos:

   * Arquivar numa conta de armazenamento
   * Transmitir em fluxo para um hub de eventos
   * Enviar para Azure Monitor Logs através de um espaço de trabalho Log Analytics

   A imagem que se segue mostra uma definição de diagnóstico que encaminha as operações nas categorias de telemetria de Conexões e Dispositivos para um espaço de trabalho Log Analytics:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Screenshot mostrando uma definição de diagnóstico completa.":::

1. Selecione **Guardar** para guardar as definições.

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no destino configurado. Para obter mais informações sobre a configuração de diagnósticos, consulte [recolher e consumir dados de registo dos seus recursos Azure.](../azure-monitor/essentials/platform-logs-overview.md)

Para obter informações mais detalhadas sobre como criar definições de diagnóstico, incluindo com o PowerShell e o Azure CLI, consulte [as definições de Diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) na documentação do Monitor Azure.

### <a name="turn-off-operations-monitoring"></a>Desligar a monitorização das operações

> [!NOTE]
> A partir de 11 de março de 2019, a funcionalidade de monitorização de operações é removida da interface do portal Azure do IoT Hub. Os degraus abaixo já não se aplicam. Para migrar, certifique-se de que as categorias corretas são encaminhada para um destino com uma definição de diagnóstico do Azure Monitor acima.

Uma vez testadas as novas definições de diagnóstico no seu fluxo de trabalho, pode desligar a função de monitorização de operações. 

1. No menu IoT Hub, selecione **Monitorização de Operações.**

2. Em cada categoria de monitorização, **selecione Nenhum**.

3. Guarde as alterações de monitorização das operações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar aplicações que utilizam monitorização de operações

Os esquemas de monitorização de operações e registos de recursos variam ligeiramente. É importante que atualize as aplicações que utilizam hoje a monitorização de operações para mapear o esquema utilizado pelos registos de recursos.

Além disso, os registos de recursos do IoT Hub oferecem cinco novas categorias para rastreio. Depois de atualizar as aplicações para o esquema existente, adicione as novas categorias também:

* Operações gémeas nuvem-para-dispositivo
* Operações gémeas dispositivo-a-nuvem
* Consultas gémeas
* Operações de tarefas
* Métodos Diretos

Para as estruturas de esquemas específicas, consulte [os registos de recursos.](monitor-iot-hub-reference.md#resource-logs)

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Dispositivo de monitorização liga e desconecta eventos com baixa latência

Para monitorizar os eventos de ligação e desconexão do dispositivo na produção, recomendamos a subscrição do [ **evento desligado**](iot-hub-event-grid.md#event-types) do dispositivo na Grelha de Eventos para obter alertas e monitorizar o estado de ligação do dispositivo. Utilize este [tutorial](iot-hub-how-to-order-connection-state-events.md) para aprender a integrar eventos ligados ao dispositivo conectados e desligados do dispositivo a partir do IoT Hub na sua solução IoT.

## <a name="next-steps"></a>Passos seguintes

[Monitorizar o Hub IoT](monitor-iot-hub.md)