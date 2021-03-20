---
title: Resolução de problemas Erro do Hub Azure IoT 404103 DeviceNotOnline
description: Entenda como corrigir o erro 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960818"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Este artigo descreve as causas e soluções para **erros 404103 DeviceNotOnline.**

## <a name="symptoms"></a>Sintomas

Um método direto para um dispositivo falha com o erro **404103 DeviceNotOnline** mesmo que o dispositivo esteja online. 

## <a name="cause"></a>Causa

Se sabe que o dispositivo está online e ainda obtém o erro, é provável que seja porque o método direto de chamada não está registado no dispositivo.

## <a name="solution"></a>Solução

Para configurar corretamente o seu dispositivo para chamadas de métodos diretos, consulte [manusear um método direto num dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).