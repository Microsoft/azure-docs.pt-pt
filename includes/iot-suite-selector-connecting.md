---
title: incluir ficheiro
description: incluir ficheiro
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751534"
---
> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C em Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (genérico)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

Neste tutorial, implementar um **Chiller** dispositivo que envia a seguinte telemetria para a monitorização remota [acelerador de solução](../articles/iot-accelerators/about-iot-accelerators.md):

* Temperatura
* Pressão
* Humidade

Para simplificar, o código gera valores de telemetria de exemplo para o **Chiller**. Pode alargar a amostra ao ligar sensores reais ao dispositivo e enviar telemetria real.

O dispositivo de exemplo também:

* Envia os metadados para a solução para descrever seus recursos.
* Responde às ações acionadas a partir da **dispositivos** página na solução.
* Enviar responde às alterações de configuração a partir da **dispositivos** página na solução.

Para concluir este tutorial, precisa de uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o seu dispositivo, implementar o acelerador de soluções de monitorização remota e adicione um novo dispositivo real à solução.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Implementar o acelerador de soluções de monitorização remota

O **Chiller** dispositivo que vai criar neste tutorial envia dados para uma instância da [monitorização remota](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) acelerador de solução. Se ainda não aprovisionou o acelerador de solução de monitorização remota na sua conta do Azure, veja [implementar o acelerador de solução de monitorização remota](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Quando concluir o processo de implantação para a solução de monitorização remota, clique em **inicie** para abrir o dashboard de solução no seu browser.

![O dashboard da solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Adicionar o seu dispositivo à solução de monitorização remota

> [!NOTE]
> Se já tiver adicionado um dispositivo na sua solução, pode ignorar este passo. No entanto, a próxima etapa exige a cadeia de ligação do dispositivo. Pode recuperar a cadeia de ligação de um dispositivo do [portal do Azure](https://portal.azure.com) ou utilizando o [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) ferramenta CLI.

Para um dispositivo para ligar ao solution accelerator, ele tem de se identificar no IoT Hub com credenciais válidas. Terá a oportunidade para guardar a cadeia de ligação do dispositivo que contém essas credenciais ao adicionar o dispositivo à solução. Incluir a cadeia de ligação do dispositivo na sua aplicação cliente mais tarde neste tutorial.

Para adicionar um dispositivo à sua solução de monitorização remota, execute os seguintes passos no **Device Explorer** página na solução:

1. Escolher **+ novo dispositivo**e, em seguida, escolha **Real** como o **tipo de dispositivo**:

    ![Adicionar um dispositivo real](media/iot-suite-selector-connecting/devicesprovision.png)

1. Introduza **físico chiller** como o ID de dispositivo. Escolha o **chave simétrica** e **chaves de geração automática** opções:

    ![Escolher opções de dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Escolha **Aplicar**. Em seguida, anote o **ID do dispositivo**, **chave primária**, e **chave primária de cadeia de ligação** valores:

    ![Obter credenciais](media/iot-suite-selector-connecting/credentials.png)

Acabou de adicionar um dispositivo real para o acelerador de solução de monitorização remota e observar a sua cadeia de ligação do dispositivo. Nas seções a seguir, implementa a aplicação de cliente que utiliza a cadeia de ligação do dispositivo para ligar à sua solução.

A aplicação cliente implementa o incorporado **Chiller** modelo do dispositivo. Um modelo de dispositivo do acelerador de solução Especifica o seguinte sobre um dispositivo:

* As propriedades que o dispositivo comunica à solução. Por exemplo, um **Chiller** dispositivo comunica informações sobre o respetivo firmware e localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um **Chiller** dispositivo envia a temperatura, humidade e valores de pressão.
* Os métodos pode agendar da solução para executar no dispositivo. Por exemplo, um **Chiller** dispositivo tem de implementar **reiniciar**, **FirmwareUpdate**, **EmergencyValveRelease**, e  **IncreasePressure** métodos.
