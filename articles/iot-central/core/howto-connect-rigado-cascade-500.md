---
title: Ligue uma Cascata Rigado 500 no Azure IoT Central [ Central De Tet] Microsoft Docs
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
ms.openlocfilehash: 3e6026e683d46ca9062c5c139fcd7febd7df9bd1
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758242"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Ligue um dispositivo de gateway Rigado Cascade 500 à sua aplicação Central Azure IoT

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este artigo descreve como, como construtor de soluções, pode ligar um dispositivo de gateway Rigado Cascade 500 à sua aplicação Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>O que é Cascade 500?

Cascade 500 IoT gateway é uma oferta de hardware da Rigado que está incluída como parte da sua solução Cascade Edge-as-a-Service. Fornece equipas comerciais de projetos IoT e produtos com poder de computação flexível de borda, um ambiente de aplicação contentorizado robusto, e uma grande variedade de opções de conectividade de dispositivos sem fios, incluindo Bluetooth 5, LTE, & Wi-Fi.

A Cascade 500 é pré-certificada para o Azure IoT Plug and Play (pré-visualização) permitindo aos nossos construtores de soluções embarcarfacilmente a bordo do dispositivo para as suas soluções finais. O gateway Cascade permite-lhe ligar sem fios a uma variedade de sensores de monitorização de condições que estão próximos do dispositivo gateway. Estes sensores podem ser a bordo da IoT Central através do dispositivo gateway.

## <a name="prerequisites"></a>Pré-requisitos
Para passar por este guia de como orientar, precisa dos seguintes recursos:

* Um dispositivo Rigado Cascade 500. Para mais informações, visite [Rigado.](https://www.rigado.com/)
* Uma aplicação Azure IoT Central. Para mais informações, consulte a [criação de uma nova aplicação.](./quick-deploy-iot-central.md)

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Para embarcar num dispositivo de gateway Cascade 500 na sua instância de aplicação Azure IoT Central, terá de configurar um modelo de dispositivo correspondente dentro da sua aplicação.

Para adicionar um modelo de dispositivo cascade 500: 

1. Navegue para o separador ***modelos*** de dispositivo ![no painel esquerdo, selecione + **Novo**: Criar novo modelo de dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. A página dá-lhe a opção de ***criar um modelo personalizado*** ou usar um modelo de dispositivo ***pré-configurado***
1. Selecione o modelo de dispositivo C500 a partir da ![lista de modelos de dispositivos pré-configurados, como mostrado abaixo: Selecione modelo de dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecione ***Seguinte: Personalize*** para continuar até ao próximo passo. 
1. No ecrã seguinte, selecione ***Criar*** para embarcar no modelo do dispositivo C500 na sua aplicação IoT Central.

## <a name="retrieve-application-connection-details"></a>Recuperar detalhes da ligação da aplicação

Terá agora de recuperar o ID de **âmbito** e a **chave Primara** para a sua aplicação Azure IoT Central para ligar o dispositivo Cascade 500. 

1. Navegue para **a Administração** no painel esquerdo e clique na **ligação**do Dispositivo . 
2. Tome nota do **ID** scope para a sua aplicação IoT Central.
![App Scope ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Agora clique em **Ver Chaves** e tome nota da chave
![primária chave **primary**](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Entrar em contato com Rigado para ligar o portal 

Para ligar o dispositivo Cascade 500 à sua aplicação IoT Central, terá de contactar o Rigado e fornecer-lhes os dados de ligação de aplicação a partir dos passos acima referidos. 

Assim que o dispositivo estiver ligado à internet, o Rigado será capaz de reduzir uma atualização de configuração para o dispositivo de gateway Cascade 500 através de um canal seguro. 

Esta atualização aplicará os detalhes de ligação IoT Central no dispositivo Cascade 500 e aparecerá na sua lista de dispositivos. 

![Chave Primária](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Está agora pronto para utilizar o seu dispositivo C500 na sua aplicação IoT Central!

## <a name="next-steps"></a>Passos seguintes

Se é um desenvolvedor de dispositivos, alguns sugeriu que os próximos passos são para:

- Ler sobre [conectividade do dispositivo na Central Azure IoT](./concepts-get-connected.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
