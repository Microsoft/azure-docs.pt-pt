---
title: Resolução de problemas Erro Do Hub Azure IoT 404103 DeviceNotOnline
description: Entenda como corrigir o erro 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960818"
---
# <a name="404103-devicenotonline"></a>404103 DispositivoSNotOnline

Este artigo descreve as causas e soluções para **erros 404103 DeviceNotOnline.**

## <a name="symptoms"></a>Sintomas

Um método direto para um dispositivo falha com o erro **404103 DeviceNotOnline** mesmo que o dispositivo esteja online. 

## <a name="cause"></a>Causa

Se sabe que o dispositivo está on-line e ainda obtém o erro, é provável que o desvio do método direto não esteja registado no dispositivo.

## <a name="solution"></a>Solução

Para configurar corretamente o seu dispositivo para chamadas de método direto, consulte [o manusear um método direto num dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).