---
title: Ligue um RuuviTag em Azure IoT Central Microsoft Docs
description: Aprenda a ligar um sensor ambiental RuuviTag à sua aplicação IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158258"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Ligue um sensor RuuviTag à sua aplicação Central Azure IoT

Este artigo descreve como, como um construtor de soluções, pode ligar um sensor RuuviTag à sua aplicação Microsoft Azure IoT Central.

O que é uma etiqueta Ruuvi?

A RuuviTag é uma plataforma avançada de sinalização de sensores de código aberto projetada para satisfazer as necessidades de clientes empresariais, desenvolvedores, criadores, estudantes e hobbyistas. O dispositivo está configurado para funcionar assim que o tirar da caixa e está pronto para o colocar onde precisar. É um farol Bluetooth LE com sensor de ambiente e acelerómetro incorporado.

O RuuviTag comunica sobre ble (Bluetooth Low Energy) e requer um dispositivo de gateway para falar com a Azure IoT Central. Certifique-se de que tem um dispositivo gateway, como o Rigado Cascade 500, configurado para permitir que um RuuviTag ligue-se à IoT Central.

Por favor, siga as [instruções aqui](./howto-connect-rigado-cascade-500.md) se quiser configurar um dispositivo de gateway Rigado Cascade 500.

## <a name="prerequisites"></a>Pré-requisitos

Para ligar os sensores RuuviTag, precisa dos seguintes recursos:

* Um sensor RuuviTag. Para mais informações, visite [o RuuviTag.](https://ruuvi.com/)
* Um dispositivo Rigado Cascade 500 ou outro portal BLE. Para mais informações, visite [Rigado.](https://www.rigado.com/)
* Uma aplicação Azure IoT Central. Para mais informações, consulte a [criação de uma nova aplicação.](./quick-deploy-iot-central.md)

## <a name="add-a-ruuvitag-device-template"></a>Adicione um modelo de dispositivo RuuviTag

Para embarcar num sensor RuuviTag na sua instância de aplicação Azure IoT Central, precisa de configurar um modelo de dispositivo correspondente dentro da sua aplicação.

Para adicionar um modelo de dispositivo RuuviTag:

1. Navegar para o separador ***modelos*** de dispositivo no painel esquerdo, selecione **+ Novo** ![: Criar novo modelo](./media/howto-connect-ruuvi/devicetemplate-new.png) de dispositivo A página dá-lhe a opção de criar um modelo ***personalizado*** ou usar um modelo de ***dispositivo pré-configurado***
1. Selecione o modelo do dispositivo RuuviTag a partir da ![lista de modelos de dispositivos pré-configurados, como mostrado abaixo: Selecione modelo de dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selecione ***Seguinte: Personalize*** para continuar até ao próximo passo.
1. No ecrã seguinte, selecione ***Criar*** para embarcar no modelo do dispositivo C500 na sua aplicação IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Ligue um sensor RuuviTag

Como mencionado anteriormente, para ligar o RuuviTag à sua aplicação IoT Central, precisa de configurar um dispositivo gateway. Os degraus abaixo assumem que criou um dispositivo de gateway Rigado Cascade 500.  

1. Ligue no seu dispositivo Rigado Cascade 500 e conecte-o à sua ligação de rede (via Ethernet ou wireless)
1. Retire a tampa do RuuviTag e puxe a lingueta de plástico para fixar a ligação com a bateria.
1. Coloque o RuuviTag perto de um gateway Rigado Cascade 500 que já está configurado na sua aplicação IoT Central.
1. Em poucos segundos, o seu RuuviTag deve aparecer na sua lista de dispositivos dentro da IoT Central.  
    ![Lista de Dispositivos RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Agora pode utilizar este RuuviTag dentro da sua aplicação IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Criar um RuuviTag simulado

Se não tiver um dispositivo RuuviTag físico, pode criar um sensor RuuviTag simulado para utilizar para testes dentro da sua aplicação Azure IoT Central.

Para criar um RuuviTag simulado:

1. Selecione **Dispositivos > RuuviTag**.
1. Selecione **+ Novo**.
1. Especifique um ID único **do dispositivo** e um nome de **dispositivo**amigável .  
1. Ativar a definição **simulada.**
1. Selecione **Criar**.  

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a ligar um RuuviTag à sua aplicação Azure IoT Central, o próximo passo sugerido é aprender a personalizar a [sua aplicação IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) para construir uma solução final.
