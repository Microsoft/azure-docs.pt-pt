---
title: Azure IoT Edge e Azure IoT Central [ Azure IoT Central] Microsoft Docs
description: Entenda como usar o Azure IoT Edge com uma aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89d068fbd835a5b4f8886a2ed77691f94a701bb0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759098"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ligue os dispositivos Azure IoT Edge a uma aplicação Central Azure IoT

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

O IoT Edge é composto por três componentes:

* **Os módulos IoT Edge** são recipientes que executam serviços Azure, serviços parceiros ou o seu próprio código. Os módulos são implantados para dispositivos IoT Edge e funcionam localmente nesses dispositivos.
* O tempo de funcionação do **IoT Edge** funciona em cada dispositivo IoT Edge e gere os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite-lhe monitorizar e gerir remotamente dispositivos IoT Edge. IoT Central é a interface da nuvem.

Um dispositivo **Azure IoT Edge** pode ser um dispositivo de gateway, com dispositivos a jusante ligados ao dispositivo IoT Edge. Este artigo partilha mais informações sobre os padrões de conectividade do dispositivo a jusante.

Um modelo de **dispositivo** define as capacidades do seu dispositivo e módulos IoT Edge. As capacidades incluem telemetria que o módulo envia, propriedades do módulo, e os comandos a que um módulo responde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações a jusante do dispositivo com um portal e módulos

Os dispositivos a jusante podem ligar-se `$edgeHub` a um dispositivo de gateway IoT Edge através do módulo. Este dispositivo IoT Edge torna-se uma porta de entrada transparente neste cenário.

![Diagrama de gateway transparente](./media/concepts-iot-edge/gateway-transparent.png)

Os dispositivos a jusante também podem ligar-se a um dispositivo de gateway IoT Edge através de um módulo personalizado. No seguinte cenário, os dispositivos a jusante conectam-se através de um módulo personalizado Modbus.

![Diagrama de conexão personalizada do módulo](./media/concepts-iot-edge/gateway-module.png)

O diagrama seguinte mostra a ligação a um dispositivo de `$edgeHub`gateway IoT Edge através de ambos os tipos de módulos (personalizado e ).  

![Diagrama de ligação através de ambos os módulos de ligação](./media/concepts-iot-edge/gateway-module-transparent.png)

Finalmente, os dispositivos a jusante podem ligar-se a um dispositivo de gateway IoT Edge através de vários módulos personalizados. O diagrama seguinte mostra dispositivos a jusante que se ligam `$edgeHub` através de um módulo personalizado Modbus, um módulo personalizado BLE e o módulo. 

![Diagrama de ligação através de vários módulos personalizados](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifestos de implantação e modelos de dispositivo

No IoT Edge, pode implementar e gerir a lógica do negócio sob a forma de módulos. Os módulos IoT Edge são a menor unidade de computação gerida pelo IoT Edge, e podem conter serviços Azure (como o Azure Stream Analytics), ou o seu próprio código específico de solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, consulte [os módulos IoT Edge](../../iot-edge/iot-edge-modules.md).

A um nível elevado, um manifesto de implantação é uma lista de gémeos módulos que são configurados com as suas propriedades desejadas. Um manifesto de implantação diz a um dispositivo IoT Edge (ou um grupo de dispositivos) quais os módulos a instalar e como configurá-los. Os manifestos de implantação incluem as propriedades desejadas para cada módulo gémeo. Os dispositivos IoT Edge relatam as propriedades reportadas para cada módulo.

Utilize o Código do Estúdio Visual para criar um manifesto de implantação. Para saber mais, consulte [Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

No Azure IoT Central, pode importar um manifesto de implantação para criar um modelo de dispositivo. O fluxograma seguinte mostra um ciclo de vida manifesto de implantação na IoT Central.

![Fluxograma de implantação do ciclo de vida manifesto](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (pré-visualização) modela um dispositivo IoT Edge da seguinte forma:

* Todos os modelos de dispositivos IoT Edge têm um modelo de capacidade de dispositivo.
* Para cada módulo personalizado listado no manifesto de implementação, é gerado um modelo de capacidade de módulo.
* Estabelece-se uma relação entre cada modelo de capacidade do módulo e um modelo de capacidade do dispositivo.
* Um modelo de capacidade de módulo implementa interfaces de módulos.
* Cada interface de módulo contém telemetria, propriedades e comandos.

![Diagrama da modelação IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivos de gateway IoT Edge

Se selecionou um dispositivo IoT Edge para ser um dispositivo de gateway, pode adicionar relações a jusante aos modelos de capacidade do dispositivo para dispositivos que pretende ligar ao dispositivo gateway.

## <a name="next-steps"></a>Passos seguintes

Se for um desenvolvedor de dispositivos, um próximo passo sugerido é aprender sobre os tipos de dispositivos gateway [em IoT Central](./tutorial-define-gateway-device-type.md).
