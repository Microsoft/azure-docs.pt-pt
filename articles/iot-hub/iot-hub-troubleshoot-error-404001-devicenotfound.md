---
title: Resolução de problemas Erro do Hub Azure IoT 404001 DeviceNotFound
description: Entenda como corrigir erro 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061286"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Este artigo descreve as causas e soluções para **erros 404001 DeviceNotFound.**

## <a name="symptoms"></a>Sintomas

Durante uma comunicação cloud-to-device (C2D), como mensagem C2D, atualização dupla ou método direto, a operação falha com o erro **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

A operação falhou porque o dispositivo não pode ser encontrado pelo IoT Hub. O aparelho não está registado ou desativado.

## <a name="solution"></a>Solução

Registe a identificação do dispositivo que usou e tente novamente.