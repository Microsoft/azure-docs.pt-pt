---
title: 'Tutorial: Criar uma app de monitorização de painéis solares com Azure IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de painel solar utilizando modelos de aplicação Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9ea1db982a6944bd12b458624545b3888881508f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881924"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Tutorial: Criar e explorar o modelo de aplicação de monitorização do painel solar 

Este tutorial guia-o através do processo de criação de uma aplicação de monitorização de painéis solares, que inclui um modelo de dispositivo de amostra com dados simulados. Neste tutorial, irá aprender a:


> [!div class="checklist"]
> * Crie gratuitamente uma aplicação de painel solar
> * Caminhe pela aplicação
> * Limpar os recursos


Se não tiver uma subscrição, [crie uma conta de teste gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

Não há pré-requisitos para completar este tutorial. Recomenda-se uma subscrição do Azure, mas não é necessária.


## <a name="create-a-solar-panel-monitoring-app"></a>Criar uma aplicação de monitorização de painéis solares 

Pode criar esta aplicação em três passos simples:

1. Vá ao [Azure IoT Central.](https://apps.azureiotcentral.com) Para criar uma nova aplicação, selecione **Build**. 

1. Selecione o **separador Energia.** Sob **monitorização do painel solar,** selecione **Create app**. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot de opções de Construção Central Azure IoT.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Na nova caixa de diálogo de **aplicações,** preencha os detalhes solicitados e, em seguida, selecione **Criar**:
    * **Nome da aplicação**: Escolha um nome para a sua aplicação Azure IoT Central. 
    * **URL**: Escolha um URL Central Azure IoT. A plataforma verifica a sua singularidade.
    * **Plano de preços**: Se já tiver uma subscrição Azure, recomenda-se a definição por defeito. Se não tiver uma assinatura Azure, comece com o teste gratuito.
    * **Informação de faturação**: A aplicação em si é gratuita. O diretório, subscrição Azure e detalhes da região são necessários para a disponibilização dos recursos para a sua app.
        ![Screenshot da nova aplicação.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Screenshot da informação de Billing.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifique a aplicação e os dados simulados

Pode modificar a sua nova aplicação de painel solar a qualquer momento. Por enquanto, certifique-se de que a aplicação está implementada e funciona como esperado antes de modificá-la.

Para verificar a criação de aplicações e simulação de dados, vá ao **Dashboard**. Se conseguir ver os azulejos com alguns dados, então a implementação da sua aplicação foi bem sucedida. A simulação de dados pode demorar alguns minutos a gerar os dados. 

## <a name="application-walk-through"></a>Walk-through de aplicação
Depois de implementar com sucesso o modelo de aplicação, vai querer explorar um pouco mais a aplicação. Note que vem com o dispositivo de medidor inteligente de amostra, modelo do dispositivo e painel de instrumentos.

A Adatum é uma empresa de energia fictícia que monitoriza e gere painéis solares. No painel de monitorização do painel solar, vê-se propriedades, dados e comandos de amostras de painéis solares. Este painel permite que você ou a sua equipa de apoio realizem as seguintes atividades de forma proactiva, antes que quaisquer problemas exijam apoio adicional:
* Reveja as informações mais recentes do painel e a sua localização instalada no mapa.
* Verifique o estado do painel e o estado da ligação.
* Reveja a produção de energia e as tendências de temperatura para capturar quaisquer padrões anómalos.
* Acompanhe a geração total de energia para fins de planeamento e faturação.
* Ativar um painel e atualizar a versão do firmware, se necessário. No modelo, os botões de comando mostram as possíveis funcionalidades e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> ![Screenshot do painel de monitorização do painel solar.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
A aplicação vem com um dispositivo de painel solar de amostra. Para ver os detalhes do dispositivo, selecione **Dispositivos**.

> [!div class="mx-imgBorder"]
> ![Screenshot dos dispositivos do modelo de monitorização do painel solar.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Selecione o dispositivo de amostra, **SP0123456789**. A partir do separador **Update Properties,** pode atualizar as propriedades do dispositivo e ver uma visão dos valores atualizados no painel de instrumentos. 

> [!div class="mx-imgBorder"]
> ![Screenshot do separador propriedades de atualização do modelo de monitorização do painel solar.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Para ver o modelo do dispositivo do painel solar, selecione o **separador modelos do dispositivo.** O modelo tem interfaces predefinidos para dados, propriedades, comandos e vistas.

> [!div class="mx-imgBorder"]
> ![Screenshot dos modelos de dispositivo de monitorização do painel solar.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Se decidir não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Do painel esquerdo, selecione **Administração**.
1. Selecione **as definições de aplicação**  >  **Delete**. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot da administração do modelo de monitorização do painel solar.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Passos seguintes
 
> [!div class="nextstepaction"]
> [Azure IoT Central - arquitetura de aplicativos de painel solar](./concept-iot-central-solar-panel-app.md)
* [Crie modelos de aplicação de painel solar gratuitamente](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* [Visão geral do Azure IoT Central](../index.yml)
