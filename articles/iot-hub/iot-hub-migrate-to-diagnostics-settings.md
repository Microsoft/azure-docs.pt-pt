---
title: Azure IoT Hub migra para definições de diagnósticos Microsoft Docs
description: Como atualizar o Azure IoT Hub para utilizar as definições de diagnóstico do Azure em vez de monitorizar as operações para monitorizar o estado das operações no seu hub IoT em tempo real.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750688"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrar o seu Hub IoT da monitorização de operações para as definições de diagnóstico

Os clientes que utilizam [a monitorização](iot-hub-operations-monitoring.md) de operações para monitorizar o estado das operações no IoT Hub podem migrar esse fluxo de trabalho para as definições de diagnóstico do [Azure,](../azure-monitor/platform/platform-logs-overview.md)uma característica do Monitor Azure. As definições de diagnóstico fornecem informações de diagnóstico ao nível de recursos para muitos serviços Azure.

**A funcionalidade de monitorização de operações do IoT Hub está deprecida**e foi removida do portal. Este artigo fornece medidas para mover as suas cargas de trabalho da monitorização de operações para as definições de diagnóstico. Para obter mais informações sobre a linha temporal de depreciação, consulte [Monitor as soluções Azure IoT com o Azure Monitor e a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Update IoT Hub

Para atualizar o seu IoT Hub no portal Azure, ligue primeiro as definições de diagnóstico e desligue a monitorização das operações.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desligue a monitorização das operações

> [!NOTE]
> A partir de 11 de março de 2019, a funcionalidade de monitorização de operações é removida da interface do portal Azure do IoT Hub. Os passos abaixo já não se aplicam. Para migrar, certifique-se de que as categorias corretas são ligadas nas definições de diagnóstico do Monitor Azure acima.

Assim que testar as novas definições de diagnóstico no seu fluxo de trabalho, pode desligar a função de monitorização de operações. 

1. No menu IoT Hub, selecione **monitorização de operações**.

2. Em cada categoria de monitorização, selecione **Nenhum**.

3. Guarde as alterações de monitorização das operações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar aplicações que utilizam monitorização de operações

Os esquemas para monitorização de operações e definições de diagnóstico variam ligeiramente. É importante que atualize as aplicações que utilizam a monitorização de operações hoje para mapear o esquema utilizado pelas definições de diagnóstico. 

Além disso, as definições de diagnóstico oferecem cinco novas categorias para rastreio. Depois de atualizar as aplicações para o esquema existente, adicione também as novas categorias:

* Operações duplas cloud-to-device
* Operações gémeas dispositivo-nuvem
* Consultas gémeas
* Operações de tarefas
* Métodos Diretos

Para as estruturas de esquemas específicas, consulte [Compreender o esquema para configurações](iot-hub-monitor-resource-health.md#understand-the-logs)de diagnóstico .

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Dispositivo de monitorização conecta e desliga eventos com baixa latência

Para monitorizar a ligação e desconexão do dispositivo em produção, recomendamos subscrever o evento desligado do [ **dispositivo** ](iot-hub-event-grid.md#event-types) na Rede de Eventos para obter alertas e monitorizar o estado de ligação do dispositivo. Utilize este [tutorial](iot-hub-how-to-order-connection-state-events.md) para aprender a integrar eventos conectados e desligados do dispositivo do IoT Hub na sua solução IoT.

## <a name="next-steps"></a>Passos seguintes

[Monitorizar o estado de funcionamento do Hub IoT do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md)
