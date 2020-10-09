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
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960831"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Este artigo descreve as causas e soluções para **erros 404001 DeviceNotFound.**

## <a name="symptoms"></a>Sintomas

Durante uma comunicação cloud-to-device (C2D), como mensagem C2D, atualização dupla ou método direto, a operação falha com o erro **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

A operação falhou porque o dispositivo não pode ser encontrado pelo IoT Hub. O aparelho não está registado ou desativado.

## <a name="solution"></a>Solução

Registe a identificação do dispositivo que usou e tente novamente.