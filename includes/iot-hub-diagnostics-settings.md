---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792047"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Ativar o registo com definições de diagnóstico

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue até ao seu hub IoT.

2. Selecione **definições de Diagnóstico**.

3. **Selecione Ligue os diagnósticos**.

   ![Ativar os diagnósticos](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Dê um nome às definições de diagnóstico.

5. Escolha para onde quer enviar os registos. Pode selecionar qualquer combinação das três opções:

   * Arquivar numa conta de armazenamento
   * Transmitir em fluxo para um hub de eventos
   * Enviar para o Log Analytics

6. Escolha quais as operações que pretende monitorizar e ative registos para essas operações. As operações que as definições de diagnóstico podem reportar são:

   * Ligações
   * Telemetria do dispositivo
   * Mensagens nuvem-para-dispositivo
   * Operações de identidade do dispositivo
   * Uploads de ficheiros
   * Encaminhamento de mensagens
   * Operações gémeas nuvem-para-dispositivo
   * Operações gémeas dispositivo-a-nuvem
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

As novas definições fazem efeito em cerca de 10 minutos. Depois disso, os registos aparecem no alvo de arquivo configurado na lâmina de **definição de diagnóstico.** Para obter mais informações sobre a configuração de diagnósticos, consulte [recolher e consumir dados de registo dos seus recursos Azure.](../articles/azure-monitor/platform/platform-logs-overview.md)
