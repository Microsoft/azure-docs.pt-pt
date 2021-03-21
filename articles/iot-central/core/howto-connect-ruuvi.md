---
title: Ligue um RuuviTag em Azure IoT Central | Microsoft Docs
description: Saiba como ligar um sensor de ambiente RuuviTag à sua aplicação IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 7cf0d31895eaeaa41c23f9f07664b27bed1d3126
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006392"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Ligue um sensor RuuviTag à sua aplicação Azure IoT Central

*Este artigo aplica-se aos construtores de soluções.*

Este artigo descreve como, como construtor de soluções, pode ligar um sensor RuuviTag à sua aplicação Microsoft Azure IoT Central.

O que é uma etiqueta Ruuvi?

A RuuviTag é uma plataforma avançada de sensores de código aberto projetada para satisfazer as necessidades de clientes empresariais, desenvolvedores, criadores, estudantes e hobbyists. O dispositivo está configurado para funcionar assim que o tirar da sua caixa e está pronto para o colocar onde precisar. É um farol Bluetooth LE com um sensor de ambiente e acelerómetro incorporado.

O RuuviTag comunica sobre ble (Bluetooth Low Energy) e requer um dispositivo de gateway para falar com a Azure IoT Central. Certifique-se de que tem um dispositivo de gateway, como o Rigado Cascade 500, configuração para permitir que um RuuviTag se conecte à IoT Central.

Por favor, siga as [instruções aqui](./howto-connect-rigado-cascade-500.md) se quiser configurar um dispositivo de gateway Rigado Cascade 500.

## <a name="prerequisites"></a>Pré-requisitos

Para ligar os sensores RuuviTag, precisa dos seguintes recursos:

* Um sensor RuuviTag. Para mais informações, visite [o RuuviTag.](https://ruuvi.com/)
* Um dispositivo Rigado Cascade 500 ou outro gateway BLE. Para mais informações, visite [Rigado.](https://www.rigado.com/)
* Uma aplicação Azure IoT Central. Para mais informações, consulte a [criação de uma nova aplicação.](./quick-deploy-iot-central.md)

## <a name="add-a-ruuvitag-device-template"></a>Adicione um modelo de dispositivo RuuviTag

Para embarcar num sensor RuuviTag na sua aplicação Azure IoT Central, é necessário configurar um modelo de dispositivo correspondente dentro da sua aplicação.

Para adicionar um modelo de dispositivo RuuviTag:

1. Navegue para os **modelos** do dispositivo _ separador no painel esquerdo, selecione _*+ Novo**: ![ Criar novo modelo de dispositivo A página ](./media/howto-connect-ruuvi/devicetemplate-new.png) dá-lhe uma opção para ***Criar um modelo personalizado**_ ou _ Use um modelo de dispositivo *_pré-configurado_**
1. Selecione o modelo de dispositivo RuuviTag da lista de modelos de dispositivo pré-configurados como mostrado abaixo:  ![ Selecione o modelo do dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecione ***Seguinte: Personalize*** para continuar até ao próximo passo.
1. No ecrã seguinte, ***selecione Criar*** para bordo o modelo do dispositivo C500 na sua aplicação IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Ligue um sensor RuuviTag

Como mencionado anteriormente, para ligar o RuuviTag à sua aplicação IoT Central, precisa de configurar um dispositivo gateway. Os passos abaixo assumem que montou um dispositivo de gateway Rigado Cascade 500.  

1. Ligue o seu dispositivo Rigado Cascade 500 e conecte-o à sua ligação de rede (via Ethernet ou sem fios)
1. Retire a tampa do RuuviTag e puxe a lingueta de plástico para fixar a ligação com a bateria.
1. Coloque o RuuviTag perto de um portal Rigado Cascade 500 que já está configurado na sua aplicação IoT Central.
1. Em apenas alguns segundos, o seu RuuviTag deve aparecer na sua lista de dispositivos dentro da IoT Central.  
    ![Lista de dispositivos RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Agora pode utilizar este RuuviTag dentro da sua aplicação IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Criar um RuuviTag simulado

Se não tiver um dispositivo RuuviTag físico, pode criar um sensor RuuviTag simulado para ser utilizado para testes dentro da sua aplicação Azure IoT Central.

Para criar um RuuviTag simulado:

1. Selecione **dispositivos > RuuviTag**.
1. Selecione **+ Novo**.
1. Especifique um **ID** de dispositivo único e um nome de **dispositivo** amigável .  
1. Ativar a **definição simulada.**
1. Selecione **Criar**.  

## <a name="next-steps"></a>Passos Seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Leia sobre [a conectividade do dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
