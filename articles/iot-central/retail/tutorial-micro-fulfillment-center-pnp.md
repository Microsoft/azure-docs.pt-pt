---
title: Tutorial de modelo de aplicativo do Centro de Micro-realização Microsoft Docs
description: Um tutorial do modelo de aplicação do centro de micro-realização para ioT central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027791"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação do centro de micro-realização

Neste tutorial, vamos aproveitar o modelo de aplicação do Centro de ***Micro-realização*** Central Azure IoT para lhe mostrar como construir uma solução de retalho. Você vai aprender sobre como implementar o modelo MFC, o que está incluído fora da caixa e o que você pode querer fazer a seguir.

Neste tutorial, ficará a saber como: 
> [!div class="checklist"]
> * Use o modelo central de **micro-realização** do Azure IoT para criar uma aplicação de retalho
> * Caminhe pela aplicação 

## <a name="prerequisites"></a>Pré-requisitos
Para completar esta série tutorial, precisa de:
* Uma subscrição do Azure. Você pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .

## <a name="create-an-application"></a>Criar uma aplicação 
Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usará esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Navegue para o site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral)
1. Se tiver uma subscrição Azure, inscreva-se com as credenciais que utiliza para aceder ao mesmo, caso contrário, inscreva-se através de uma conta Microsoft:

   ![Introduza a sua conta da organização](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **Retail**.  A página de varejo apresenta vários modelos de aplicação de retalho.

Para criar uma nova aplicação do centro de micro-realização que utiliza funcionalidades de pré-visualização:  
1. Selecione o modelo de aplicação do **centro de micro-realização.** Este modelo inclui modelos de dispositivo para todos os dispositivos utilizados no tutorial. O modelo também fornece um painel de instrumentos do operador para monitorizar as condições dentro do seu centro de realização, bem como as condições para os seus portadores robóticos. 

    > [!div class="mx-imgBorder"]
    > ![](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png) tipo de aplicação de micro-realização
    
1. Opcionalmente, escolha um nome de **aplicação**amigável.  O modelo de aplicação baseia-se na empresa fictícia Northwind Traders. 

    > [!NOTE]
    > Se utilizar um nome de **aplicação**amigável, ainda deve utilizar um valor único para o **URL**da aplicação .

1. Se tiver uma assinatura Azure, insira o seu *Diretório, subscrição Azure e Região.* Se não tiver uma subscrição, pode ativar um **teste gratuito de 7** dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre as subscrições e diretórios, veja [criar uma aplicação de início rápido](../preview/quick-deploy-iot-central.md).

1. Selecione **Criar**.

> [!div class="mx-imgBorder"]
> ![Criar](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png) de aplicações de micro-realização

## <a name="walk-through-the-application"></a>andar através da aplicação 

### <a name="dashboard"></a>Dashboard 

Depois de implementar com sucesso o modelo de aplicação, você irá primeiro aterrar no painel de micro-realização do Centro de **Micro-Realização de Traders de Northwind**. Northwind Trader's é um retalhista fictício que tem um centro de micro-realização sendo gerido nesta aplicação IoT Central. Neste painel de instrumentos do operador, você verá informações e telemetria sobre os dispositivos neste modelo juntamente com um conjunto de comandos, empregos e ações que você pode tomar. O painel de instrumentos é logicamente dividido em duas secções (esquerda e direita). À esquerda, tem a capacidade de monitorizar as condições ambientais dentro da estrutura de realização e à direita, pode monitorizar a saúde de um transportador robótico dentro da instalação.  

A partir do painel de instrumentos pode:
   * Consulte a telemetria do dispositivo como # de picks, # de encomendas processadas, e propriedades como o estado do sistema de estrutura, etc.  
   * Veja a planta do **piso** e a localização dos transportadores robóticos dentro da estrutura de realização.
   * Acionar comandos como a reposição do sistema de controlo, a atualização do firmware da transportadora, a reconfiguração da rede, etc.

> [!div class="mx-imgBorder"]
> ![centro de micro-realização](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Veja um exemplo do painel de instrumentos que um operador pode aproveitar para monitorizar as condições dentro do centro de realização. 
   * Monitorize a saúde das cargas que estão a funcionar no dispositivo de gateway dentro do centro de realização.    

> [!div class="mx-imgBorder"]
> ![centro de micro-realização](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Se clicar no separador de modelos do Dispositivo, verá que existem dois tipos diferentes de dispositivos que fazem parte do modelo: 
   * **Transportadora Robótica**: Este modelo de dispositivo representa a definição para um transportador robótico funcional que foi implantado na estrutura de realização e está a realizar operações adequadas de armazenamento e recuperação. Se clicar no modelo, verá que o robô está a enviar dados do dispositivo, como temperatura, posição do eixo e propriedades como o estado do portador robótico, etc. 
   * **Monitorização da Condição da Estrutura**: Este modelo de dispositivo representa uma recolha de dispositivos que lhe permite monitorizar a condição ambiental, bem como o dispositivo gateway que acolhe várias cargas de trabalho de borda para alimentar o seu centro de realização. O dispositivo envia dados de telemetria como a temperatura, # de palhetas, # de encomendas, etc. para além do estado e saúde das cargas de trabalho computacionais em funcionamento no seu ambiente. 

> [!div class="mx-imgBorder"]
> ![modelos de dispositivo sintetmentismo de micro-realização](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Se clicar no separador de grupos do Dispositivo, verá também que estes modelos de dispositivo saem automaticamente de grupos de dispositivos criados para os mesmos.

## <a name="rules"></a>Regras
Ao saltar para o separador de regras, verá uma regra de amostra que existe no modelo de aplicação para monitorizar as condições de temperatura para o transportador robótico. Pode utilizar esta regra para alertar o operador se um robô específico da instalação estiver a sobreaquecer e tiver de ser desligado para manutenção. 

Por favor, aproveite a regra da amostra como inspiração para definir regras mais adequadas para as suas funções comerciais.

   - **Transportadora robótica demasiado quente**: Esta regra irá desencadear se o transportador robótico atingir um limiar de temperatura durante um período de tempo. 

> [!div class="mx-imgBorder"]
> ![regras do centro de micro-realização](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o modelo de aplicação visitando **as definições** de > Aplicação **da Administração** e clique em **Eliminar**.

> [!div class="mx-imgBorder"]
> ![](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png) de limpeza de aplicações do centro de micro-realização

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura de solução de [centro de micro-realização](./architecture-micro-fulfillment-center-pnp.md)
* Saiba mais sobre outros [modelos de retalho IoT Central](./overview-iot-central-retail-pnp.md)
* Saiba mais sobre ioT Central consulte [a visão geral da IoT Central](../preview/overview-iot-central.md)