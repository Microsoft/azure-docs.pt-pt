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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960766"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para **erros de DeviceMessageLockLost 412002.**

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem nuvem-para-dispositivo, o pedido falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem nuvem-dispositivo da fila (por exemplo, [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) utilizando), a mensagem é bloqueada pelo IoT Hub durante um período de tempo de bloqueio de um minuto. Se o dispositivo tentar completar a mensagem após o fim do tempo de bloqueio, o IoT Hub lança esta exceção.

## <a name="solution"></a>Solução

Se o IoT Hub não receber a notificação dentro do prazo de um minuto, ele devolve a mensagem ao estado *enqueuso.* O dispositivo pode tentar receber novamente a mensagem. Para evitar que o erro aconteça no futuro, implemente a lógica do lado do dispositivo para completar a mensagem no prazo de um minuto após receber a mensagem. Este intervalo de um minuto não pode ser alterado.