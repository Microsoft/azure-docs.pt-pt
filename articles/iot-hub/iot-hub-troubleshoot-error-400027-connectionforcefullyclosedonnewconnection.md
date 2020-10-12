---
title: Resolução de problemas Erro do Hub Azure IoT 400027 ConnectionForcefullyClosedOnNewConnection
description: Entenda como corrigir erro 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960532"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para **400027 Erros de ConexãoForcedamenteClosedOnNewConnection.**

## <a name="symptoms"></a>Sintomas

A sua operação de dois dispositivos para nuvem (como propriedades relatadas de leitura ou correção) ou invocação de método direto falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova ligação ao IoT Hub usando as mesmas credenciais, por isso o IoT Hub fechou a ligação anterior. O IoT Hub não permite que mais de um cliente se conecte usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecta ao IoT Hub utilizando a sua própria identidade.