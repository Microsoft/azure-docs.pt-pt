---
title: Resolução de problemas Erro do Hub Azure IoT 409001 DeviceAlreadyExists
description: Entenda como corrigir o erro 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960792"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Este artigo descreve as causas e soluções para **erros de 409001 DeviceAlreadyExists.**

## <a name="symptoms"></a>Sintomas

Ao tentar registar um dispositivo no IoT Hub, o pedido falha com o erro **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Já há um dispositivo com o mesmo dispositivo ID no hub IoT. 

## <a name="solution"></a>Solução

Use uma identificação de dispositivo diferente e tente novamente.