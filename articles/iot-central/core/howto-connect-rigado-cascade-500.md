---
title: Ligue uma Cascata Rigado 500 no Azure IoT Central Microsoft Docs
description: Saiba como ligar um dispositivo de gateway Rigado Cascade 500 à sua aplicação IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016848"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Ligue um dispositivo de gateway Rigado Cascade 500 à sua aplicação Azure IoT Central

*Este artigo aplica-se aos construtores de soluções.*

Este artigo descreve como, como construtor de soluções, pode ligar um dispositivo de gateway Rigado Cascade 500 à sua aplicação Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>O que é Cascade 500?

Cascade 500 IoT gateway é uma oferta de hardware do Rigado que está incluída como parte da sua solução Cascade Edge-as-a-Service. Fornece equipas comerciais de projeto ioT e produtos com potência de computação de borda flexível, um ambiente robusto de aplicação contentorizada, e uma grande variedade de opções de conectividade de dispositivos sem fios, incluindo Bluetooth 5, LTE, & Wi-Fi.

A Cascade 500 está pré-certificada para Azure IoT Plug and Play (pré-visualização) permitindo que os nossos construtores de soluções acedam facilmente ao dispositivo para as suas soluções finais. O gateway Cascade permite-lhe ligar-se sem fios a uma variedade de sensores de monitorização de condições que estão próximos do dispositivo gateway. Estes sensores podem ser colocados a bordo na IoT Central através do dispositivo gateway.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia, precisa dos seguintes recursos:

* Um dispositivo Rigado Cascade 500. Para mais informações, visite [Rigado.](https://www.rigado.com/)
* Uma aplicação Azure IoT Central. Para mais informações, consulte a [criação de uma nova aplicação.](./quick-deploy-iot-central.md)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Para embarcar num dispositivo de gateway Cascade 500 na sua aplicação Azure IoT Central, terá de configurar um modelo de dispositivo correspondente dentro da sua aplicação.

Para adicionar um modelo de dispositivo Cascade 500: 

1. Navegue para o ***separador Modelos de Dispositivo*** no painel esquerdo, selecione **+ Novo**: Criar novo modelo de ![ dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. A página dá-lhe uma opção para ***criar um modelo personalizado*** ou usar um modelo de dispositivo ***pré-configurado***
1. Selecione o modelo do dispositivo C500 da lista de modelos de dispositivo pré-configurados como mostrado abaixo: ![ Selecione o modelo do dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecione ***Seguinte: Personalize*** para continuar até ao próximo passo. 
1. No ecrã seguinte, ***selecione Criar*** para bordo o modelo do dispositivo C500 na sua aplicação IoT Central.

## <a name="retrieve-application-connection-details"></a>Recuperar detalhes da ligação da aplicação

Terá agora de recuperar o **ID** de âmbito e **a chave primária** para a sua aplicação Azure IoT Central para ligar o dispositivo Cascade 500. 

1. Navegue para a **Administração**  no painel esquerdo e clique na **ligação do dispositivo**. 
2. Tome nota do **ID do âmbito** para a sua aplicação IoT Central.
![ID de âmbito de aplicação](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Agora clique em **Ver Chaves** e tome nota da **chave** 
 ![ primária primária](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Entrar em contato com Rigado para ligar o portal 

Para ligar o dispositivo Cascade 500 à sua aplicação IoT Central, terá de contactar rigado e fornecer-lhes os detalhes da ligação da aplicação a partir dos degraus acima. 

Uma vez que o dispositivo esteja ligado à internet, Rigado será capaz de empurrar uma atualização de configuração para baixo para o dispositivo de gateway Cascade 500 através de um canal seguro. 

Esta atualização aplicará os detalhes da ligação IoT Central no dispositivo Cascade 500 e aparecerá na lista de dispositivos. 

![Lista de dispositivos](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Está agora pronto para usar o seu dispositivo C500 na sua aplicação IoT Central!

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Leia sobre [a conectividade do dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
