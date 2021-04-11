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
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061371"
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