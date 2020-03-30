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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184695"
---
> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C em Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (genérico)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

Neste tutorial, implementa um dispositivo **Chiller** que envia a seguinte telemetria para o acelerador de [solução](../articles/iot-accelerators/about-iot-accelerators.md)de monitorização remota:

* Temperatura
* Pressão
* Humidade

Para a simplicidade, o código gera valores de telemetria de amostra para o **Chiller**. Pode estender a amostra ligando sensores reais ao seu dispositivo e enviando uma telemetria real.

O dispositivo de amostra também:

* Envia metadados para a solução para descrever as suas capacidades.
* Responde às ações desencadeadas a partir da página **dispositivos** na solução.
* Responde às alterações de configuração enviadas da página **dispositivos** na solução.

Para concluir este tutorial, precisa de uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o seu dispositivo, implante o acelerador da solução de monitorização remota e adicione um novo dispositivo real à solução.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Implante o seu acelerador de solução de monitorização remota

O dispositivo **Chiller** que cria neste tutorial envia dados para uma instância do acelerador de solução de [monitorização remota.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Se ainda não disponibilizou o acelerador de soluções de monitorização remota na sua conta Azure, consulte [O acelerador de soluções](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) de monitorização remota

Quando o processo de implementação da solução de Monitorização Remota terminar, clique em **Lançar** para abrir o painel de solução no seu navegador.

![O painel de solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Adicione o seu dispositivo à solução de monitorização remota

> [!NOTE]
> Se já adicionou um dispositivo na sua solução, pode saltar este passo. No entanto, o próximo passo requer a corda de ligação do dispositivo. Pode recuperar a cadeia de ligação de um dispositivo a partir do [portal Azure](https://portal.azure.com) ou utilizando a ferramenta [AZ iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI.

Para que um dispositivo se conecte ao acelerador de soluções, deve identificar-se ao IoT Hub utilizando credenciais válidas. Tem a oportunidade de guardar a cadeia de ligação do dispositivo que contém estas credenciais quando adiciona o dispositivo à solução. Você inclui a cadeia de ligação do dispositivo na sua aplicação de cliente mais tarde neste tutorial.

Para adicionar um dispositivo à sua solução de Monitorização Remota, complete os seguintes passos na página do Explorer do **Dispositivo** na solução:

1. Escolha **+ Novo dispositivo,** e depois escolha **Real** como o **tipo dispositivo:**

    ![Adicionar um dispositivo real](media/iot-suite-selector-connecting/devicesprovision.png)

1. Introduza o **refrigerador físico** como o ID do dispositivo. Escolha as opções de chaves de **chave simétrica** e **automática:**

    ![Escolha opções de dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Escolha **Aplicar**. Em seguida, tome nota do ID do **dispositivo,** **chave primária,** e valores principais da cadeia de **ligação:**

    ![Recuperar credenciais](media/iot-suite-selector-connecting/credentials.png)

Adicionou agora um dispositivo real ao acelerador de soluções de monitorização remota e observou a sua cadeia de ligação ao dispositivo. Nas seguintes secções, implementa a aplicação do cliente que utiliza a cadeia de ligação do dispositivo para se ligar à sua solução.

A aplicação do cliente implementa o modelo de dispositivo **Chiller** incorporado. Um modelo de dispositivo acelerador de solução especifica o seguinte sobre um dispositivo:

* As propriedades que o dispositivo reporta à solução. Por exemplo, um dispositivo **Chiller** reporta informações sobre o seu firmware e localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um dispositivo **Chiller** envia valores de temperatura, humidade e pressão.
* Os métodos que pode agendar a partir da solução para executar no dispositivo. Por exemplo, um dispositivo **Chiller** deve implementar métodos **reboot**, **FirmwareUpdate,** **EmergencyValveRelease**e **IncreasePressure.**
