---
title: Conectar um RuuviTag no Azure IoT Central | Microsoft Docs
description: Saiba como conectar um sensor de ambiente RuuviTag ao seu aplicativo IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: a068ea3f51e4faa6497c7b1a93192a924753c7c9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895101"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Conectar um sensor RuuviTag ao seu aplicativo IoT Central do Azure

Este artigo descreve como, como um Solution Builder, você pode conectar um sensor RuuviTag ao seu aplicativo Microsoft Azure IoT Central.

O que é uma marca Ruuvi?

O RuuviTag é uma plataforma avançada de Beacon de sensor de software livre projetada para atender às necessidades de clientes comerciais, desenvolvedores, criadores, estudantes e entusiastas. O dispositivo está configurado para funcionar assim que você o retirar da caixa e estiver pronto para implantá-lo onde for necessário. Trata-se de um Beacon LE Bluetooth com um sensor de ambiente e o acelerômetro integrado.

RuuviTag comunica-se em BLE (Bluetooth de baixa energia) e requer um dispositivo de gateway para se comunicar com o IoT Central do Azure. Verifique se você tem um dispositivo de gateway, como o rigado Cascade 500, a instalação para permitir que um RuuviTag se conecte ao IoT Central.

Siga as [instruções aqui](./howto-connect-rigado-cascade-500.md) se desejar configurar um dispositivo de gateway 500 em cascata rigado.

## <a name="prerequisites"></a>Pré-requisitos

Para conectar sensores do RuuviTag, você precisa dos seguintes recursos:

* Um sensor de RuuviTag. Para obter mais informações, visite [RuuviTag](https://ruuvi.com/).
* Um dispositivo rigado em cascata 500 ou outro gateway BLE. Para obter mais informações, visite [rigado](https://www.rigado.com/).
* Um aplicativo IoT Central do Azure criado a partir de um dos modelos de aplicativo de visualização. Para obter mais informações, consulte [criar um novo aplicativo](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Adicionar um modelo de dispositivo RuuviTag

Para integrar um sensor RuuviTag em sua instância do aplicativo IoT Central do Azure, você precisa configurar um modelo de dispositivo correspondente em seu aplicativo.

Para adicionar um modelo de dispositivo RuuviTag:

1. Navegue até a ***guia modelos de dispositivo*** no painel esquerdo, selecione **+ novo**: ![criar novo modelo de dispositivo](./media/howto-connect-ruuvi/devicetemplate-new.png) a página oferece uma opção para ***criar um modelo personalizado*** ou ***usar um modelo de dispositivo pré-configurado***
1. Selecione o modelo de dispositivo RuuviTag na lista de modelos de dispositivo pré-configurados, conforme mostrado abaixo: ![selecionar modelo de dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecione ***Avançar: Personalizar*** para continuar na próxima etapa.
1. Na próxima tela, selecione ***criar*** para carregar o modelo de dispositivo C500 em seu aplicativo IOT central.

## <a name="connect-a-ruuvitag-sensor"></a>Conectar um sensor RuuviTag

Conforme mencionado anteriormente, para conectar o RuuviTag ao seu aplicativo IoT Central, você precisa configurar um dispositivo de gateway. As etapas a seguir pressupõem que você configurou um dispositivo de gateway rigado Cascade 500 em cascata.  

1. Ligue o dispositivo rigado Cascade 500 e conecte-o à sua conexão de rede (via Ethernet ou sem fio)
1. Retire a tampa do RuuviTag e puxe a guia plástica para proteger a conexão com a bateria.
1. Coloque o RuuviTag próximo a um gateway de 500 em cascata rigado que já esteja configurado em seu aplicativo IoT Central.
1. Em apenas alguns segundos, seu RuuviTag deve aparecer na lista de dispositivos dentro de IoT Central.  
    ![Lista de dispositivos RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Agora você pode usar esse RuuviTag em seu aplicativo IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Criar um RuuviTag simulado

Se você não tiver um dispositivo físico RuuviTag, poderá criar um sensor de RuuviTag simulado a ser usado para teste em seu aplicativo de IoT Central do Azure.

Para criar um RuuviTag simulado:

1. Selecione **dispositivos > RuuviTag**.
1. Selecione **+ novo**.
1. Especifique uma **ID de dispositivo** exclusiva e um **nome de dispositivo**amigável.  
1. Habilite a configuração **simulada** .
1. Selecione **Criar**.  

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como conectar um RuuviTag ao seu aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [personalizar seu aplicativo IOT central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) para criar uma solução de ponta a ponta.
