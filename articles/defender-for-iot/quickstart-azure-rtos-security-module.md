---
title: 'Quickstart: Configurar e ativar o Defender-IoT-micro-agente para Azure RTOS'
description: Aprenda a bordo e ative o serviço Defender-IoT-micro-agente para o serviço Azure RTOS no seu Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 3c1af1128b99cbd3263ddffc834eb27ab9dec564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489850"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Quickstart: Defender-IoT-micro-agente para Azure RTOS (pré-visualização)

Este artigo fornece uma explicação dos pré-requisitos antes de começar e explica como ativar o serviço Defender-IoT-micro-agent para o serviço Azure RTOS num Hub IoT. Se não tiver atualmente um Hub IoT, consulte [Criar um Hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md) para começar.

## <a name="prerequisites"></a>Pré-requisitos 

### <a name="supported-devices"></a>Dispositivos suportados

- Kit de descoberta STM32F746G
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Faça o download, compile e execute um dos ficheiros .zip para o quadro e ferramenta específicos (IAR, semi's IDE ou PC) da sua escolha a partir do [defender-ioT-micro-agente para o recurso Azure RTOS GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Recursos do Azure

A próxima fase para começar é preparar os seus recursos Azure. Você vai precisar de um hub IoT e sugerimos um espaço de trabalho Log Analytics. Para o IoT Hub, você precisará da sua cadeia de ligação IoT Hub para ligar ao seu dispositivo. 
  
### <a name="iot-hub-connection"></a>Conexão IoT Hub

É necessária uma ligação IoT Hub para começar. 

1. Abra o seu **Hub IoT** no portal Azure.

1. Navegue para **dispositivos IoT**.

1. Selecione **Criar**.

1. Copie o fio de ligação IoT para o [ficheiro de configuração](how-to-azure-rtos-security-module.md).

As credenciais de ligação são retiradas da configuração da aplicação do utilizador **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

O Defender-IoT-micro-agente para Azure RTOS utiliza ligações de middleware Azure IoT com base no protocolo **MQTT.**

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para terminar a configuração e personalização da sua solução.

> [!div class="nextstepaction"]
> [Configure e personalize o defender-ioT-micro-agente para Azure RTOS (pré-visualização)](how-to-azure-rtos-security-module.md)
