---
title: Resolução de problemas Erro do Hub Azure IoT 409002 LinkCreationConflict
description: Entenda como corrigir o erro 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: c354eed6e7bf9f2147f8b81a82a06e80a9337c84
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061048"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Este artigo descreve as causas e soluções para **erros de LinkCreationConflict 409002.**

## <a name="symptoms"></a>Sintomas

Vê o erro **409002 LinkCreationConflict** em registos juntamente com a desconexão do dispositivo ou falha de mensagens nuvem-dispositivo.

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

Geralmente, este erro acontece quando o IoT Hub deteta que um cliente tem mais do que uma ligação. De facto, quando um novo pedido de ligação chega para um dispositivo com uma ligação existente, o IoT Hub fecha a ligação existente com este erro.

### <a name="cause-1"></a>Motivo 1

No caso mais comum, um problema separado (como [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) faz com que o dispositivo se desligue. O dispositivo tenta restabelecer a ligação imediatamente, mas o IoT Hub ainda considera o dispositivo ligado. O IoT Hub fecha a ligação anterior e regista este erro.

### <a name="cause-2"></a>Motivo 2

A lógica defeituosa do lado do dispositivo faz com que o dispositivo estabeleça a ligação quando um já está aberto.

## <a name="solution"></a>Solução

Este erro geralmente aparece como um efeito colateral de um problema diferente e transitório, por isso procure outros erros nos registos para resolver ainda mais os problemas. Caso contrário, certifique-se de emitir um novo pedido de ligação apenas se a ligação cair.
