---
title: Problemas de resolução de problemas Azure IoT Hub erro 409001 Dispositivo Já Existe
description: Entenda como corrigir o erro 409001 DispositivoJáExiste
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960792"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Este artigo descreve as causas e soluções para **o dispositivo 409001Exista** erros.

## <a name="symptoms"></a>Sintomas

Ao tentar registar um dispositivo no IoT Hub, o pedido falha com o erro **409001 DispositivoJáExiste**.

## <a name="cause"></a>Causa

Já há um dispositivo com o mesmo dispositivo de identificação no centro ioT. 

## <a name="solution"></a>Solução

Utilize um ID de dispositivo diferente e tente novamente.