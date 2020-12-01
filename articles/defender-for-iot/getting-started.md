---
title: Opções de implementação
description: Iniciou a compreensão do fluxo básico de trabalho do Defender para funcionalidades e serviços IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 6aa525fd7f2d82194baa2e2a0c910cb71509c2d5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340021"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Começando com Azure Defender para IoT

Este artigo descreve os processos de implantação e de embarque necessários para que o Azure Defender para o IoT seja operado. São também necessários passos adicionais. Recomenda-se que compreenda estes passos e se familiarize com informações nos documentos que o acompanham.

Assim que completar todos os passos, o Azure Defender para sensores IoT monitorizará a sua rede. Dependendo da forma como configura a sua solução, as deteções também podem ser enviadas para a consola de gestão no local, ou para o IoT Hub.

Complete os seguintes passos para obter Azure Defender para IoT em funcionamento.

## <a name="1-set-up-azure"></a>1. Configurar o Azure

- Crie uma conta Azure. Para mais informações, consulte [Criar uma conta Azure.](/learn/modules/create-an-azure-account/)

- Firewall ou proxy: Se tiver uma firewall ou um dispositivo de rede de intervenção semelhante configurado para permitir ligações específicas, verifique se ou *.azure-devices.net:443 é aberta para a firewall ou proxy. Se os wildcards não forem suportados ou se quiser mais controlo, o IoT Hub FQDN específico deve ser aberto no seu FW ou proxy. Para obter mais informações, consulte [pontos finais reference - IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md).

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. Implementar hardware, software e a bordo para sensor

- Compre hardware de sensor e instale software. Siga os passos aqui descritos e para mais informações, consulte este artigo e o Defender para o Guia de [Hardware IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) e o [Guia de Instalação.](https://aka.ms/AzureDefenderforIoTInstallSensorISO)

  - Depois de instalar o seu sensor, grave as credenciais de entrada do sensor. Vai precisar das credenciais para carregar o ficheiro de ativação para o sensor.

  - Se estiver a trabalhar com sensores geridos localmente, grave o endereço IP do sensor ou o nome do sensor definido na instalação. É melhor usá-lo ao criar um nome de sensor durante o registo do sensor no portal Defender para IoT. Pode utilizá-las mais tarde para garantir um rastreio mais fácil e um nome consistente entre o nome de registo no portal Azure Defender para IoT e o endereço IP do sensor implantado apresentado na consola do sensor.

- Registe o sensor com o portal Defender para IoT e descarregue um ficheiro de ativação de sensores.

- Faça o upload do ficheiro de ativação para o seu sensor.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. Realizar a configuração da rede para monitorização e gestão de sensores

- Ligue o seu sensor à rede. Descrito no [guia de configuração da Rede](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. Comece a descobrir a sua rede

- Ajuste as definições do sistema na consola do sensor.

- Ligue os sensores a uma consola de gestão no local.

Para obter mais informações, consulte o [Azure Defender para o Guia do Utilizador do Sensor IoT](https://aka.ms/AzureDefenderforIoTUserGuide) e o [Guia do Utilizador da consola de gestão IoT no local](https://aka.ms/DefenderForIoTManagementConsole).

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. Populate Azure Sentinel com informações de alerta

- Para enviar informações de alerta ao Azure Sentinel, configuure Azure Sentinel: [Ligue os seus dados do Defender para IoT ao Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Passos seguintes

- Ativar [o Defender para IoT](quickstart-onboard-iot-hub.md)
- Configure a sua [solução](quickstart-configure-your-solution.md)
- [Criar módulos de segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implementar um agente de segurança](how-to-deploy-agent.md)