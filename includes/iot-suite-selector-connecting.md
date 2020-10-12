---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184695"
---
> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C em Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (genérico)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

Neste tutorial, implementa-se um dispositivo **Chiller** que envia a seguinte telemetria para o acelerador de solução de [monitorização](../articles/iot-accelerators/about-iot-accelerators.md)remota:

* Temperatura
* Pressão
* Humidade

Para simplificar, o código gera valores de telemetria de amostra para o **Refrigerador**. Pode estender a amostra ligando sensores reais ao seu dispositivo e enviando telemetria real.

O dispositivo de amostra também:

* Envia metadados para a solução para descrever as suas capacidades.
* Responde às ações desencadeadas a partir da página **dispositivos** na solução.
* Responde às alterações de configuração enviadas a partir da página **dispositivos** na solução.

Para concluir este tutorial, precisa de uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o seu dispositivo, coloque o acelerador de solução de Monitorização Remota e adicione um novo dispositivo real à solução.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Implemente o seu acelerador de solução de monitorização remota

O dispositivo **Chiller** que cria neste tutorial envia dados para uma instância do acelerador de solução [de monitorização remota.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Se ainda não disponibilizou o acelerador de solução de monitorização remota na sua conta Azure, consulte [implementar o acelerador de solução de monitorização remota](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Quando o processo de implementação da solução de Monitorização Remota terminar, clique em **Lançamento** para abrir o painel de instrumentos de solução no seu navegador.

![O painel de solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Adicione o seu dispositivo à solução de Monitorização Remota

> [!NOTE]
> Se já adicionou um dispositivo na sua solução, pode saltar este passo. No entanto, o próximo passo requer a cadeia de ligação do dispositivo. Pode recuperar a cadeia de ligação de um dispositivo a partir do [portal Azure](https://portal.azure.com) ou utilizando a ferramenta [Az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI.

Para que um dispositivo se conecte ao acelerador de solução, deve identificar-se ao IoT Hub utilizando credenciais válidas. Tem a oportunidade de guardar a cadeia de ligação do dispositivo que contém estas credenciais quando adiciona o dispositivo à solução. Inclui a cadeia de ligação do dispositivo na aplicação do seu cliente mais tarde neste tutorial.

Para adicionar um dispositivo à sua solução de Monitorização Remota, complete os seguintes passos na página **do Explorador** do Dispositivo na solução:

1. Escolha **+ novo dispositivo,** e depois escolha **Real** como tipo **dispositivo**:

    ![Adicionar um dispositivo real](media/iot-suite-selector-connecting/devicesprovision.png)

1. Introduza **o refrigerador físico** como o ID do dispositivo. Escolha a **chave simétrica** e as opções **de teclas de geração automática:**

    ![Escolha as opções do dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Escolha **Aplicar**. Em seguida, tome nota dos valores de chave primária do **dispositivo, ID,** **chave primária**e **ligação:**

    ![Obter credenciais](media/iot-suite-selector-connecting/credentials.png)

Adicionou agora um dispositivo real ao acelerador de solução de monitorização remota e observou a sua cadeia de ligação do dispositivo. Nas secções seguintes, implementa a aplicação do cliente que utiliza o fio de ligação do dispositivo para se ligar à sua solução.

A aplicação do cliente implementa o modelo de dispositivo **Chiller** incorporado. Um modelo de dispositivo acelerador de solução especifica o seguinte sobre um dispositivo:

* As propriedades que o dispositivo reporta à solução. Por exemplo, um dispositivo **Chiller** informa informações sobre o seu firmware e localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um dispositivo **Chiller** envia valores de temperatura, humidade e pressão.
* Os métodos que pode agendar a partir da solução para executar no dispositivo. Por exemplo, um dispositivo **Chiller** deve implementar métodos **Reboot**, **FirmwareUpdate,** **EmergencyValveRelease**e **IncreasePressure.**
