---
title: Resolução de problemas Azure IoT Hub erro 504101 GatewayTimeout
description: Entenda como corrigir o erro 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960675"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Este artigo descreve as causas e soluções para **504101 Erros GatewayTimeout.**

## <a name="symptoms"></a>Sintomas

Ao tentar invocar um método direto do IoT Hub para um dispositivo, o pedido falha com o erro **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

IoT Hub encontrou um erro e não conseguiu confirmar se o método direto foi concluído antes de o timing.

### <a name="cause-2"></a>Causa 2

Ao utilizar uma versão anterior do Azure IoT C# SDK (<1.19.0), a ligação AMQP entre o dispositivo e o IoT Hub pode ser deixada silenciosamente por causa de um bug.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Emita uma nova tentativa.

### <a name="solution-2"></a>Solução 2

Atualize para a versão mais recente C# do Azure IOT SDK.