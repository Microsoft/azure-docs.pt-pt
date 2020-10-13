---
title: Tutorial de modelo de aplicação do centro de micro-realização Microsoft Docs
description: Um tutorial sobre o modelo de aplicação do centro de micro-realização para Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: d6b5d337090ebd664e855be47128e76d3c61e151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840443"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação do centro de micro-realização

Neste tutorial, você usa o modelo de aplicação do centro de micro-cumprimento Azure IoT Central para construir uma solução de retalho. Aprende-se como implementar o modelo, o que está incluído nele e o que pode querer fazer a seguir.

## <a name="prerequisites"></a>Pré-requisitos
Para completar esta série tutorial, precisa de uma subscrição do Azure. Pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição do Azure, pode criar uma na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Criar uma aplicação 
Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usará esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Aceda ao site do [gestor de aplicações Azure IoT Central.](https://aka.ms/iotcentral)
1. Se tiver uma assinatura Azure, inscreva-se com as credenciais que utiliza para aceder à sua base. Caso contrário, inscreva-se utilizando uma conta Microsoft:

   ![Screenshot da caixa de diálogo de entrada de conta da Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **Varejo**.  A página de varejo apresenta vários modelos de aplicações de varejo.

Para criar uma nova aplicação do centro de micro-realização que utiliza funcionalidades de pré-visualização:  
1. Selecione o modelo de aplicação **do centro de micro-cumprimento.** Este modelo inclui modelos de dispositivo para todos os dispositivos utilizados no tutorial. O modelo também fornece um painel de monitorização do operador para as condições de monitorização dentro do seu centro de realização, bem como as condições para os seus transportadores robóticos. 

    ![Screenshot de Azure IoT Central Construa a sua página de aplicação IoT](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Opcionalmente, escolha um nome de **aplicação**amigável. O modelo de aplicação baseia-se na empresa fictícia Northwind Traders. 

    >[!NOTE]
    >Se utilizar um nome de aplicação amigável, ainda deve utilizar um valor único para o URL da aplicação.

1. Se tiver uma subscrição do Azure, insira o seu diretório, subscrição Azure e região. Se não tiver uma subscrição, pode ativar um teste gratuito de 7 dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre diretórios e subscrições, consulte o Criar um quickstart de [aplicação.](../preview/quick-deploy-iot-central.md)

1. Selecione **Criar**.

    ![Screenshot de Azure IoT Central Nova página de aplicação](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Caminhe pela aplicação 

Depois de implementar com sucesso o modelo de aplicação, você vê o **painel de micro-realização do Northwind Traders**. A Northwind Traders é um retalhista fictício que tem um centro de micro-realização a ser gerido nesta aplicação Azure IoT Central. Neste painel de instrumentos do operador, você vê informações e telemetria sobre os dispositivos neste modelo, juntamente com um conjunto de comandos, empregos e ações que você pode tomar. O painel é logicamente dividido em duas secções. À esquerda, pode monitorizar as condições ambientais dentro da estrutura de cumprimento, e à direita, pode monitorizar a saúde de um transportador robótico dentro das instalações.  

A partir do painel de instrumentos, pode:
   * Consulte a telemetria do dispositivo, como o número de palhetas, o número de encomendas processadas e propriedades, como o estado do sistema de estrutura.  
   * Veja a planta do piso e a localização dos portadores robóticos dentro da estrutura de realização.
   * Os comandos de gatilho, tais como a reposição do sistema de controlo, a atualização do firmware da transportadora e a reconfiguração da rede.

     ![Screenshot da metade superior do painel de micro-realização northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Veja um exemplo do painel de instrumentos que um operador pode utilizar para monitorizar as condições dentro do centro de realização. 
   * Monitorize a saúde das cargas que estão a funcionar no dispositivo de gateway dentro do centro de realização.    

     ![Screenshot da metade botton do centro de micro-realização northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Se selecionar o separador modelos do dispositivo, verá que existem dois tipos de dispositivos diferentes que fazem parte do modelo: 
   * **Porta-robóticos**: Este modelo de dispositivo representa a definição para um transportador robótico funcional que foi implantado na estrutura de cumprimento, e está a realizar operações de armazenamento e recuperação adequadas. Se selecionar o modelo, verá que o robô está a enviar dados do dispositivo, como a posição da temperatura e do eixo, e propriedades como o estado do transportador robótico. 
   * **Monitorização da condição da estrutura**: Este modelo de dispositivo representa uma coleção de dispositivos que lhe permite monitorizar a condição do ambiente, bem como o dispositivo de gateway que hospeda várias cargas de trabalho de borda para alimentar o seu centro de realização. O dispositivo envia dados de telemetria, tais como a temperatura, o número de palhetas e o número de encomendas. Também envia informações sobre o estado e a saúde das cargas de trabalho computacional que correm no seu ambiente. 

     ![Modelos de dispositivo de centro de micro-realização](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Se selecionar o separador grupos de dispositivos, também vê que estes modelos de dispositivo têm automaticamente grupos de dispositivos criados para eles.

## <a name="rules"></a>Regras
No separador **Regras,** vê uma regra de amostra que existe no modelo de aplicação para monitorizar as condições de temperatura do transportador robótico. Pode utilizar esta regra para alertar o operador se um robô específico na instalação estiver sobreaquecido e tiver de ser desligado para manutenção. 

Utilize a regra da amostra como inspiração para definir regras mais adequadas às suas funções empresariais.

![Screenshot do separador Regras](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o modelo de aplicação. Aceda **Administration**às  >  **definições de Aplicação da**Administração e selecione **Delete**.

![Screenshot da página de definições de aplicação do centro de micro-realização](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Passos seguintes

Mais informações sobre
> [!div class="nextstepaction"]
> [micro-realização centro solução arquitetura](./architecture-micro-fulfillment-center.md)
* Saiba mais sobre os [modelos de retalho Azure IoT Central](./overview-iot-central-retail.md)
* Saiba mais sobre [a Azure IoT Central](../preview/overview-iot-central.md)
