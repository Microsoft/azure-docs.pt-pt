---
title: Resolução de problemas Erro do Hub Azure IoT 403006 DispositivoMaximumActiveFileUploadLimitExteded
description: Compreender como corrigir erro 403006 DispositivoMaximumActiveFileUploadLimitExitEded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061303"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Este artigo descreve as causas e soluções para **403006 DeviceMaximumActiveFileUploadUpitExitExceed.**

## <a name="symptoms"></a>Sintomas

O pedido de upload de ficheiros falha com o código de erro **403006** e uma mensagem "O número de pedidos de upload de ficheiros ativos não pode exceder 10".

## <a name="cause"></a>Causa

Cada cliente de dispositivo está limitado a [10 uploads de ficheiros simultâneos](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Pode facilmente ultrapassar o limite se o seu dispositivo não notificar o IoT Hub quando os uploads de ficheiros estiverem concluídos. Este problema é geralmente causado por uma rede lateral de dispositivos pouco fiável.

## <a name="solution"></a>Solução

Certifique-se de que o dispositivo pode notificar prontamente [a conclusão do upload do ficheiro IoT Hub](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Em seguida, tente [reduzir o TTL de token SAS para configuração de upload de ficheiros](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre uploads de ficheiros, consulte [upload de ficheiros uploads de ficheiros IoT Hub](./iot-hub-devguide-file-upload.md) e [Configure IoT Hub utilizando o portal Azure](./iot-hub-configure-file-upload.md).