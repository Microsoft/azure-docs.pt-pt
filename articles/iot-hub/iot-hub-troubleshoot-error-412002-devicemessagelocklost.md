---
title: Resolução de problemas Erro do Hub Azure IoT 412002 DeviceMessageLockLost
description: Entenda como corrigir o erro 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76960766"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para **erros 412002 DeviceMessageLockLost.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem cloud-to-device, o pedido falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem cloud-to-device da [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)fila (por exemplo, utilizando) a mensagem é bloqueada pelo IoT Hub durante um tempo de paragem de um minuto. Se o dispositivo tentar completar a mensagem após o tempo de bloqueio expirar, o IoT Hub lança esta exceção.

## <a name="solution"></a>Solução

Se o IoT Hub não receber a notificação dentro da duração do tempo de bloqueio de um minuto, a mensagem volta para o estado *enqueued.* O dispositivo pode tentar voltar a receber a mensagem. Para evitar que o erro aconteça no futuro, implemente a lógica lateral do dispositivo para completar a mensagem no prazo de um minuto após receber a mensagem. Este intervalo de um minuto não pode ser alterado.