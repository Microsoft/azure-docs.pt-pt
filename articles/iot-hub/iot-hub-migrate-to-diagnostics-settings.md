---
title: Migrar do IoT Hub do Azure para as definições de diagnóstico | Documentos da Microsoft
description: Como atualizar o IoT Hub do Azure para utilizar as definições de diagnóstico do Azure em vez de operações de monitorização para monitorizar o estado das operações no hub IoT em tempo real.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: kgremban
ms.openlocfilehash: d54b109cd5ef41665e595e7b878c077732536297
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674247"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrar o seu IoT Hub a partir de operações de monitorização para as definições de diagnóstico

Clientes que utilizam [monitorização de operações](iot-hub-operations-monitoring.md) controlar o estado das operações no IoT Hub pode migrar esse fluxo de trabalho [as definições de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md), uma funcionalidade do Azure Monitor. As definições de diagnóstico fornecem informações de diagnóstico ao nível do recurso para muitos serviços do Azure.

As operações de funcionalidade de monitorização do IoT Hub foi preterida e será removida no futuro. Este artigo fornece passos para mover as cargas de trabalho de monitorização para as definições de diagnóstico de operações. Para obter mais informações sobre a linha cronológica de preterição, consulte [monitorizar as soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Atualizar o IoT Hub

Para atualizar o seu IoT Hub no portal do Azure, primeiro ative as definições de diagnóstico, em seguida, desative a monitorização de operações.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desativar a monitorização de operações

Depois de testar as novas definições de diagnóstico no seu fluxo de trabalho, pode desativar as funcionalidade de monitorização de operações. 

1. No menu do IoT Hub, selecione **monitorização de operações**.

2. Em cada categoria de monitorização, selecione **None**.

3. Guarde as operações de monitorização das alterações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar aplicações que utilizam a monitorização de operações

Os esquemas para a monitorização de operações e as definições de diagnóstico são ligeiramente diferentes. É importante que Atualize as aplicações que utilizam atuais de monitorização de operações para mapear para o esquema usado pelas definições de diagnóstico. 

Além disso, as definições de diagnóstico oferece cinco categorias de novo para o controlo. Depois de Atualizar aplicativos para o esquema existente, adicione as novas categorias:

* Operações de cloud para o dispositivo duplo
* Operações de gémeos de dispositivo para a cloud
* Consultas de gémeos
* Operações de tarefas
* Métodos diretos

Para as estruturas de esquema específico, consulte [compreender o esquema para definições de diagnóstico](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitorização do dispositivo se ligar e desligar os eventos com baixa latência

Para monitorizar o dispositivo se ligar e desligar os eventos, é recomendável subscrever a [ **dispositivo desligado** eventos](iot-hub-event-grid.md#event-types) no Event Grid para receber alertas e monitorizar o estado de ligação do dispositivo. Utilize esta opção [tutorial](iot-hub-how-to-order-connection-state-events.md) para saber como integrar eventos de dispositivo ligado e desligado de dispositivo do IoT Hub na sua solução de IoT.

## <a name="next-steps"></a>Passos Seguintes

[Monitorizar o estado de funcionamento do Hub IoT do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md)
