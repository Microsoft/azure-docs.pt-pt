---
title: Resolução de problemas Azure IoT Hub erro 409002 LinkCreationConflict
description: Entenda como corrigir o erro 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758750"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Este artigo descreve as causas e soluções para **erros do LinkCreationConflict 409002.**

## <a name="symptoms"></a>Sintomas

Vê o erro **409002 LinkCreationConflict** registado em registos de diagnóstico juntamente com a desconexão do dispositivo ou falha de mensagem cloud-to-device. 

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

Geralmente, este erro acontece quando o IoT Hub deteta que um cliente tem mais do que uma ligação. De facto, quando um novo pedido de ligação chega para um dispositivo com uma ligação existente, o IoT Hub fecha a ligação existente com este erro.

### <a name="cause-1"></a>Causa 1

No caso mais comum, um problema separado (como [o 404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) faz com que o dispositivo se desconecte. O dispositivo tenta restabelecer imediatamente a ligação, mas o IoT Hub ainda considera o dispositivo ligado. O IoT Hub fecha a ligação anterior e regista este erro.

### <a name="cause-2"></a>Causa 2

A lógica do lado do dispositivo defeituosa faz com que o dispositivo estabeleça a ligação quando já está aberto.

## <a name="solution"></a>Solução

Este erro geralmente aparece como um efeito colateral de um problema diferente e transitório, por isso procure outros erros nos registos para resolver ainda mais problemas. Caso contrário, certifique-se de emitir um novo pedido de ligação apenas se a ligação cair.
