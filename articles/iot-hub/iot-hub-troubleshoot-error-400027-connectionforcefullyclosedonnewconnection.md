---
title: Problemas de resolução de problemas Erro do Hub Azure IoT 400027 ConnectionForcefullyClosedOnNewConnection
description: Entenda como corrigir o erro 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960532"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para **erros de Ligação400227 ConnectionForcefullyClosedOnNewConnection.**

## <a name="symptoms"></a>Sintomas

A operação de dispositivo-para-nuvem twin (como propriedades de leitura ou patch reportadas) ou a invocação do método direto falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova ligação ao IoT Hub usando as mesmas credenciais, pelo que o IoT Hub fechou a ligação anterior. O IoT Hub não permite que mais do que um cliente se conectem usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecta ao IoT Hub utilizando a sua própria identidade.