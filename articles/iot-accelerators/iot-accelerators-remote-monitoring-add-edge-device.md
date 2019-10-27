---
title: Solução de monitoramento remoto Adicionar dispositivo de borda-Azure | Microsoft Docs
description: Este artigo descreve como adicionar um dispositivo de IoT Edge a um acelerador de solução de monitoramento remoto
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965377"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Adicionar um dispositivo IoT Edge ao acelerador de solução de monitoramento remoto

Para adicionar um dispositivo [IOT Edge](../iot-edge/about-iot-edge.md) ao Solution Accelerator, conclua as duas etapas a seguir:

1. Adicione o dispositivo de borda na página de **Device Explorer** na interface do usuário da Web de monitoramento remoto do Solution Accelerator.
1. Instale o IoT Edge tempo de execução em seu dispositivo de borda.

## <a name="add-the-iot-edge-device"></a>Adicionar o dispositivo IoT Edge

Para adicionar um dispositivo IoT Edge ao acelerador de solução de monitoramento remoto, navegue até a página **Device Explorer** na interface do usuário da Web e clique em **+ novo dispositivo**.

No painel **novo dispositivo** , escolha **IOT Edge dispositivo**. Você pode deixar os valores padrão para as outras configurações. Em seguida, clique em **Aplicar**:

![Adicionar dispositivo IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Maneiras alternativas de adicionar um dispositivo IoT Edge

Também é possível registrar um dispositivo IoT Edge diretamente com a instância do Hub IoT no Solution Accelerator. Você precisa saber o nome do Hub IoT em seu Solution Accelerator antes de seguir qualquer um destes guias de instruções:

- [Registrar um novo dispositivo de Azure IoT Edge da portal do Azure](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Registrar um novo dispositivo de Azure IoT Edge com CLI do Azure](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Registrar um novo dispositivo de Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Quando você registra um dispositivo diretamente com o Hub IoT no acelerador de solução de monitoramento remoto, ele é listado na página de **Device Explorer** na interface do usuário da Web.

## <a name="install-the-iot-edge-runtime"></a>Instalar o IoT Edge Runtime

Antes de implantar módulos no dispositivo de borda, você deve instalar o IoT Edge tempo de execução no dispositivo real. Os guias de instruções a seguir mostram como instalar o tempo de execução em plataformas de dispositivo comuns:

- [Instalar o tempo de execução de Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalar Azure IoT Edge tempo de execução no Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instalar Azure IoT Edge tempo de execução no Windows para usar com contêineres do Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instalar o Azure IoT Edge Runtime no Windows para usar com contêineres do Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instalar o tempo de execução de IoT Edge no Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Passos seguintes

Agora que você preparou seu dispositivo IoT Edge, a próxima etapa é implantar módulos nele. Consulte [importar um pacote de IOT Edge para o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-import-edge-package.md)
