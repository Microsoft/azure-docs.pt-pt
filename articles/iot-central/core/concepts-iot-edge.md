---
title: Azure IoT Edge e Azure IoT Central Microsoft Docs
description: Entenda como usar a Azure IoT Edge com uma aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 4c4248246be845aa8608aac8b8a3ce761ea1ef86
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987053"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Ligar dispositivos Azure IoT Edge a uma aplicação do Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

IoT Edge é composto por três componentes:

* **Os módulos IoT Edge** são contentores que gerem serviços Azure, serviços de parceiros ou o seu próprio código. Os módulos são implantados em dispositivos IoT Edge e executados localmente nesses dispositivos.
* O **tempo de execução IoT Edge** funciona em cada dispositivo IoT Edge e gere os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem permite-lhe** monitorizar e gerir remotamente dispositivos IoT Edge. IoT Central é a interface de nuvem.

Um dispositivo **Azure IoT Edge** pode ser um dispositivo de gateway, com dispositivos a jusante a ligarem-se ao dispositivo IoT Edge. Este artigo partilha mais informações sobre padrões de conectividade do dispositivo a jusante.

Um **modelo de dispositivo** define as capacidades do seu dispositivo e módulos IoT Edge. As capacidades incluem telemetria que o módulo envia, propriedades do módulo, e os comandos a que um módulo responde.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relações de dispositivo a jusante com uma porta de entrada e módulos

Os dispositivos a jusante podem ligar-se a um dispositivo de gateway IoT Edge através do `$edgeHub` módulo. Este dispositivo IoT Edge torna-se uma porta de entrada transparente neste cenário.

![Diagrama de gateway transparente](./media/concepts-iot-edge/gateway-transparent.png)

Os dispositivos a jusante também podem ligar-se a um dispositivo de gateway IoT Edge através de um módulo personalizado. No cenário seguinte, os dispositivos a jusante conectam-se através de um módulo personalizado Modbus.

![Diagrama de conexão personalizada do módulo](./media/concepts-iot-edge/gateway-module.png)

O diagrama que se segue mostra a ligação a um dispositivo de gateway IoT Edge através de ambos os tipos de módulos (personalizados e `$edgeHub` ).  

![Diagrama de ligação através de ambos os módulos de ligação](./media/concepts-iot-edge/gateway-module-transparent.png)

Finalmente, os dispositivos a jusante podem ligar-se a um dispositivo de gateway IoT Edge através de vários módulos personalizados. O diagrama a seguir mostra dispositivos a jusante que se conectam através de um módulo personalizado Modbus, um módulo personalizado BLE e o `$edgeHub` módulo. 

![Diagrama de ligação através de vários módulos personalizados](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifestos de implantação e modelos de dispositivo

No IoT Edge, pode implementar e gerir a lógica de negócio sob a forma de módulos. Os módulos IoT Edge são a unidade de computação mais pequena gerida pela IoT Edge, e podem conter serviços Azure (como a Azure Stream Analytics), ou o seu próprio código específico para solução. Para entender como os módulos são desenvolvidos, implantados e [mantidos, consulte os módulos IoT Edge](../../iot-edge/iot-edge-modules.md).

A um nível elevado, um manifesto de implantação é uma lista de gémeos módulos que são configurados com as suas propriedades desejadas. Um manifesto de implantação diz a um dispositivo IoT Edge (ou a um grupo de dispositivos) quais os módulos a instalar e como os configurar. Os manifestos de implantação incluem as propriedades desejadas para cada módulo gémeo. Os dispositivos IoT Edge reportam as propriedades reportadas para cada módulo.

Utilize o Código do Estúdio Visual para criar um manifesto de implantação. Para saber mais, consulte [Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

No Azure IoT Central, pode importar um manifesto de implantação para criar um modelo de dispositivo. O fluxograma que se segue mostra um ciclo de vida manifesto de implantação na IoT Central.

![Fluxograma de implantação ciclo de vida manifesto](./media/concepts-iot-edge/dmflow.png)

IoT Central modela um dispositivo IoT Edge da seguinte forma:

* Cada modelo de dispositivo IoT Edge tem um modelo de dispositivo.
* Para cada módulo personalizado listado no manifesto de implementação, é gerado um modelo de capacidade do módulo.
* Estabelece-se uma relação entre cada modelo de capacidade do módulo e um modelo de dispositivo.
* Um modelo de capacidade de módulo implementa interfaces de módulos.
* Cada interface de módulo contém telemetria, propriedades e comandos.

![Diagrama de modelação IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Dispositivos de gateway IoT Edge

Se selecionou um dispositivo IoT Edge para ser um dispositivo de gateway, pode adicionar relações a jusante aos modelos de dispositivos para dispositivos que pretende ligar ao dispositivo gateway.

## <a name="next-steps"></a>Próximos passos

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é aprender sobre [os tipos de dispositivos gateway na IoT Central](./tutorial-define-gateway-device-type.md).
