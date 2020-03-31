---
title: Resolução de problemas Erro do Hub Azure IoT 403002 IoTHubQuotaExceeded
description: Entenda como corrigir o erro 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961117"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Este artigo descreve as causas e soluções para **erros 403002 IoTHubQuotaExceeded.**

## <a name="symptoms"></a>Sintomas

Todos os pedidos ao IoT Hub falham com o erro **403002 IoTHubQuotaExceeded**. No portal Azure, a lista de dispositivos do hub IoT não carrega.

## <a name="cause"></a>Causa

A quota diária de mensagem para o centro IoT é ultrapassada. 

## <a name="solution"></a>Solução

[Atualize ou aumente o número de unidades no hub IoT](iot-hub-upgrade.md) ou aguarde o próximo dia UTC para que a quota diária amelhore.

## <a name="next-steps"></a>Passos seguintes

* Para entender como as operações são contadas para a quota, tais como consultas gémeas e métodos diretos, ver [Preços do Hub De Compreender IoT](iot-hub-devguide-pricing.md#charges-per-operation)
* Para configurar a monitorização para o uso diário das quotas, instale um alerta com o número total métrico *de mensagens utilizadas*. Para instruções passo a passo, consulte [Configurar métricas e alertas com o Hub IoT](tutorial-use-metrics-and-diags.md#set-up-metrics)