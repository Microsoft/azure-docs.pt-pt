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
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750704"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Ativar o registo com definições de diagnóstico

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Selecione **definições de Diagnóstico**.

3. Selecione **Ligar os diagnósticos**.

   ![Ativar os diagnósticos](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Dê um nome às definições de diagnóstico.

5. Escolha para onde pretende enviar os registos. Pode selecionar qualquer combinação das três opções:

   * Arquivar numa conta de armazenamento
   * Transmitir em fluxo para um hub de eventos
   * Enviar para o Log Analytics

6. Escolha quais as operações que pretende monitorizar e ativar registos para essas operações. As operações que as definições de diagnóstico podem reportar são:

   * Ligações
   * Telemetria do dispositivo
   * Mensagens cloud-to-device
   * Operações de identidade do dispositivo
   * Uploads de ficheiros
   * Encaminhamento de mensagens
   * Operações duplas cloud-to-device
   * Operações gémeas dispositivo-nuvem
   * Operações gémeas
   * Operações de trabalho
   * Métodos diretos  
   * Rastreio distribuído (pré-visualização)
   * Configurações
   * Fluxos de dispositivos
   * Métricas do dispositivo

6. Guarde as novas definições. 

Se pretender ligar as definições de diagnóstico com o PowerShell, utilize o seguinte código:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

As novas definições têm efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado na lâmina de definições de **Diagnóstico.** Para obter mais informações sobre a configuração de diagnósticos, consulte [Recolher e consumir dados de registo dos seus recursos azure](../articles/azure-monitor/platform/platform-logs-overview.md).
