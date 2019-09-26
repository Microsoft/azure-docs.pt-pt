---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266828"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Habilitar log com configurações de diagnóstico

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Selecione **configurações de diagnóstico**.

3. Selecione **ativar os diagnósticos**.

   ![Ativar diagnósticos](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Dê um nome às configurações de diagnóstico.

5. Escolha onde você deseja enviar os logs. Você pode selecionar qualquer combinação das três opções:

   * Arquivar numa conta de armazenamento
   * Transmitir em fluxo para um hub de eventos
   * Enviar para o Log Analytics

6. Escolha quais operações você deseja monitorar e habilite os logs para essas operações. As operações nas quais as configurações de diagnóstico podem ser relatadas são:

   * Ligações
   * Telemetria do dispositivo
   * Mensagens da cloud para dispositivo
   * Operações de identidade do dispositivo
   * Carrega o ficheiro
   * Encaminhamento de mensagens
   * Operações de entrelaçamento da nuvem para o dispositivo
   * Operações de entrelaçamento do dispositivo para a nuvem
   * Operações de entrelaçamento
   * Operações de trabalho
   * Métodos diretos  
   * Rastreamento distribuído (visualização)
   * Configurações
   * Fluxos de dispositivo
   * Métricas do dispositivo

6. Salve as novas configurações. 

Se você quiser ativar as configurações de diagnóstico com o PowerShell, use o seguinte código:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Novas definições entrem em vigor em cerca de 10 minutos. Depois disso, os logs aparecem no destino de arquivamento configurado na folha **configurações de diagnóstico** . Para obter mais informações sobre como configurar o diagnóstico, consulte [coletar e consumir dados de log dos recursos do Azure](../articles/azure-monitor/platform/resource-logs-overview.md).
