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
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94506333"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para **400027 Erros de ConexãoForcedamenteClosedOnNewConnection.**

## <a name="symptoms"></a>Sintomas

O seu dispositivo desliga-se com **Communication_Error** como **ConnectionStatusChangeReason** utilizando o tipo de transporte .NET SDK e MQTT.

A sua operação de dois dispositivos para nuvem (como propriedades relatadas de leitura ou correção) ou invocação de método direto falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova ligação ao IoT Hub usando as mesmas credenciais, por isso o IoT Hub fechou a ligação anterior. O IoT Hub não permite que mais de um cliente se conecte usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecta ao IoT Hub utilizando a sua própria identidade.