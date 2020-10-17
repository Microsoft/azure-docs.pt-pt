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
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144268"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para **erros de DeviceMessageLockLost 412002.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem nuvem-para-dispositivo, o pedido falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem nuvem-dispositivo da fila (por exemplo, [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) utilizando), a mensagem é bloqueada pelo IoT Hub durante um período de tempo de bloqueio de um minuto. Se o dispositivo tentar completar a mensagem após o fim do tempo de bloqueio, o IoT Hub lança esta exceção.

## <a name="solution"></a>Solução

Se o IoT Hub não receber a notificação dentro do prazo de um minuto, ele devolve a mensagem ao estado *enqueuso.* O dispositivo pode tentar receber novamente a mensagem. Para evitar que o erro aconteça no futuro, implemente a lógica do lado do dispositivo para completar a mensagem no prazo de um minuto após receber a mensagem. Este intervalo de um minuto não pode ser alterado.