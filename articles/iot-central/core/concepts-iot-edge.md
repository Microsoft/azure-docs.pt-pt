---
title: Azure IoT Edge e Azure IoT Central [ Azure IoT Central] Microsoft Docs
description: Entenda como usar o Azure IoT Edge com uma aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027072"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ligue os dispositivos Azure IoT Edge a uma aplicação Central Azure IoT

IoT Edge é composta por três componentes:

* Os **módulos de IOT Edge** são contêineres que executam serviços do Azure, serviços de parceiros ou seu próprio código. Os módulos são implantados em dispositivos IoT Edge e executados localmente nesses dispositivos.
* O **tempo de execução do IOT Edge** é executado em cada dispositivo IOT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite que você monitore e gerencie dispositivos IOT Edge remotamente. IoT Central é a interface de nuvem.

Um dispositivo **Azure IOT Edge** pode ser um dispositivo de gateway, com dispositivos downstream conectando-se ao dispositivo IOT Edge. Este artigo partilha mais informações sobre os padrões de conectividade do dispositivo a jusante.

Um **modelo de dispositivo** define os recursos do seu dispositivo e módulos de IOT Edge. Os recursos incluem telemetria que o módulo envia, propriedades do módulo e os comandos que um módulo responde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo downstream com um gateway e módulos

Os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio do módulo `$edgeHub`. Esse IoT Edge dispositivo torna-se um gateway transparente nesse cenário.

![Diagrama de gateway transparente](./media/concepts-iot-edge/gateway-transparent.png)

Os dispositivos downstream também podem se conectar a um dispositivo de gateway IoT Edge por meio de um módulo personalizado. No cenário a seguir, os dispositivos downstream se conectam por meio de um módulo personalizado Modbus.

![Diagrama da conexão de módulo personalizada](./media/concepts-iot-edge/gateway-module.png)

O diagrama a seguir mostra a conexão a um dispositivo de gateway IoT Edge por meio de ambos os tipos de módulos (personalizado e `$edgeHub`).  

![Diagrama de conexão por meio de ambos os módulos de conexão](./media/concepts-iot-edge/gateway-module-transparent.png)

Por fim, os dispositivos downstream podem se conectar a um dispositivo de gateway IoT Edge por meio de vários módulos personalizados. O diagrama a seguir mostra os dispositivos downstream que se conectam por meio de um módulo personalizado MODBUS, um módulo BLE personalizado e o módulo `$edgeHub`. 

![Diagrama de conexão por meio de vários módulos personalizados](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifestos de implantação e modelos de dispositivo

No IoT Edge, você pode implantar e gerenciar a lógica de negócios na forma de módulos. Os módulos de IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (como Azure Stream Analytics) ou seu próprio código específico da solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, consulte [módulos IOT Edge](../../iot-edge/iot-edge-modules.md).

Num alto nível, um manifesto de implantação é uma lista de duplos de módulo que estão configurados com as respetivas propriedades pretendidas. Um manifesto de implantação informa um dispositivo IoT Edge (ou um grupo de dispositivos) quais módulos instalar e como configurá-los. Os manifestos de implantação incluem as propriedades desejadas para cada módulo. IoT Edge dispositivos reportam as propriedades relatadas para cada módulo.

Use Visual Studio Code para criar um manifesto de implantação. Para saber mais, confira [Azure IOT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

No Azure IoT Central, pode importar um manifesto de implantação para criar um modelo de dispositivo. O fluxograma a seguir mostra um ciclo de vida de manifesto de implantação no IoT Central.

![Fluxograma do ciclo de vida de manifesto de implantação](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (pré-visualização) modela um dispositivo IoT Edge da seguinte forma:

* Cada modelo de dispositivo IoT Edge tem um modelo de funcionalidade de dispositivo.
* Para cada módulo personalizado listado no manifesto de implantação, um modelo de funcionalidade de módulo é gerado.
* Uma relação é estabelecida entre cada modelo de funcionalidade de módulo e um modelo de capacidade de dispositivo.
* Um modelo de funcionalidade de módulo implementa interfaces de módulo.
* Cada interface de módulo contém telemetria, propriedades e comandos.

![Diagrama de modelagem de IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivos de gateway IoT Edge

Se você selecionou um dispositivo de IoT Edge para ser um dispositivo de gateway, poderá adicionar relações de downstream a modelos de capacidade de dispositivo para dispositivos que você deseja conectar ao dispositivo de gateway.

## <a name="next-steps"></a>Passos seguintes

Agora que você sabe o que são os modelos de aplicativo IoT Central, comece [criando um aplicativo IOT central](quick-deploy-iot-central.md).