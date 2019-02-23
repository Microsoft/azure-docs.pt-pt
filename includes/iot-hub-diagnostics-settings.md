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
ms.openlocfilehash: 6f7772eb7f2c500bbb58c391b1bc4b7a73141699
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675760"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Ativar o registo com as definições de diagnóstico

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Selecione **as definições de diagnóstico**.

3. Selecione **ativar os diagnósticos**.

   ![Ativar os diagnósticos](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Dê um nome às definições de diagnóstico.

5. Escolha onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:

   * Arquivar numa conta de armazenamento
   * Transmitir em fluxo para um hub de eventos
   * Enviar para o Log Analytics

6. Escolher as operações que pretende monitorizar e ativar os registos para essas operações. As operações que podem reportar as definições de diagnóstico são:

   * Ligações
   * Telemetria do dispositivo
   * Mensagens da cloud para dispositivo
   * Operações de identidade de dispositivo
   * Carrega o ficheiro
   * Encaminhamento de mensagens
   * Operações de cloud para o dispositivo duplo
   * Operações de gémeos de dispositivo para a cloud
   * Operações de duplo
   * Operações de tarefa
   * Métodos diretos  
   * Rastreio distribuído (pré-visualização)
   * Configurações
   * Fluxos de dispositivo
   * Métricas de dispositivo

6. Guarde as definições de novo. 

Se pretender ativar as definições de diagnóstico com o PowerShell, utilize o seguinte código:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Novas definições entrem em vigor em cerca de 10 minutos. Depois disso, os registos de aparecem no arquivo de destino configurado no **as definições de diagnóstico** painel. Para obter mais informações sobre como configurar diagnósticos, consulte [recolher e consumir dados de registo dos seus recursos do azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).