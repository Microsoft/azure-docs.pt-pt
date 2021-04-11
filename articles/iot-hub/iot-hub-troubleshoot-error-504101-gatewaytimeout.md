---
title: Resolução de problemas Erro do Hub Azure IoT 504101 GatewayTimeout
description: Entenda como corrigir o erro 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060946"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Este artigo descreve as causas e soluções para **erros gatewayTimeout 504101.**

## <a name="symptoms"></a>Sintomas

Ao tentar invocar um método direto do IoT Hub para um dispositivo, o pedido falha com o erro **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Motivo 1

O IoT Hub encontrou um erro e não conseguiu confirmar se o método direto foi concluído antes do tempo de 200.

### <a name="cause-2"></a>Motivo 2

Ao utilizar uma versão anterior do Azure IoT C# SDK (<1.19.0), a ligação AMQP entre o dispositivo e o IoT Hub pode ser largada silenciosamente por causa de um bug.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Emita uma repetição.

### <a name="solution-2"></a>Solução 2

Upgrade para a versão mais recente do Azure IOT C# SDK.