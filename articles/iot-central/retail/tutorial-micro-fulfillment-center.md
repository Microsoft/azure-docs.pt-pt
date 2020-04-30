---
title: Tutorial de modelo de aplicativo do centro de micro-realização Microsoft Docs
description: Um tutorial sobre o modelo de aplicação do centro de micro-realização para a Central Azure IoT
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 74deb4253a21445e21f7ef04f53f3bfe3f1fe0d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000540"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Tutorial: Implementar e caminhar através de um modelo de aplicação do centro de micro-realização

Neste tutorial, você usa o modelo de aplicação do centro de micro-realização Azure IoT Central para construir uma solução de retalho. Aprende-se como implementar o modelo, o que está incluído nele, e o que talvez queira fazer a seguir.

## <a name="prerequisites"></a>Pré-requisitos
Para completar esta série tutorial, precisa de uma subscrição Azure. Você pode opcionalmente usar um teste gratuito de 7 dias. Se não tiver uma subscrição Azure, pode criar uma na [página de inscrição](https://aka.ms/createazuresubscription)do Azure .

## <a name="create-an-application"></a>Criar uma aplicação 
Nesta secção, você cria uma nova aplicação Azure IoT Central a partir de um modelo. Você usará esta aplicação ao longo da série tutorial para construir uma solução completa.

Para criar uma nova aplicação do Azure IoT Central:

1. Aceda ao site do gestor de [aplicações Azure IoT Central.](https://aka.ms/iotcentral)
1. Se tiver uma assinatura Azure, inscreva-se nas credenciais que utiliza para aceder ao mesmo. Caso contrário, inscreva-se utilizando uma conta Microsoft:

   ![Screenshot da caixa de diálogo de registo da conta da Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Para começar a criar uma nova aplicação do Azure IoT Central, selecione **New Application** (Nova Aplicação).

1. Selecione **Retail**.  A página de varejo apresenta vários modelos de aplicação de retalho.

Para criar uma nova aplicação do centro de micro-realização que utiliza funcionalidades de pré-visualização:  
1. Selecione o modelo de aplicação do **centro de micro-realização.** Este modelo inclui modelos de dispositivo para todos os dispositivos utilizados no tutorial. O modelo também fornece um painel de instrumentos do operador para monitorizar as condições dentro do seu centro de realização, bem como as condições para os seus portadores robóticos. 

    ![Screenshot do Azure IoT Central Construa a sua página de aplicação IoT](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Opcionalmente, escolha um nome de **aplicação**amigável. O modelo de aplicação baseia-se na empresa fictícia Northwind Traders. 

    >[!NOTE]
    >Se utilizar um nome de aplicação amigável, ainda deve utilizar um valor único para o URL da aplicação.

1. Se tiver uma subscrição Azure, insira o seu diretório, subscrição Azure e região. Se não tiver uma subscrição, pode ativar um teste gratuito de 7 dias e completar as informações de contacto necessárias.  

    Para obter mais informações sobre diretórios e subscrições, consulte o [Create a application](../preview/quick-deploy-iot-central.md) quickstart.

1. Selecione **Criar**.

    ![Screenshot da nova página de aplicação do Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Caminhe pela aplicação 

Depois de implementar com sucesso o modelo de aplicação, você vê o painel de micro-realização do Centro de **Micro-Realização dos Traders de Northwind**. A Northwind Traders é um retalhista fictício que tem um centro de micro-realização a ser gerido nesta aplicação Azure IoT Central. Neste painel de instrumentos do operador, você vê informações e telemetria sobre os dispositivos neste modelo, juntamente com um conjunto de comandos, empregos e ações que você pode tomar. O painel de instrumentos é logicamente dividido em duas secções. À esquerda, pode monitorizar as condições ambientais dentro da estrutura de realização e, à direita, pode monitorizar a saúde de um transportador robótico dentro da instalação.  

Do painel de instrumentos, pode:
   * Consulte a telemetria do dispositivo, como o número de escolhas, o número de encomendas processadas e propriedades, como o estado do sistema de estrutura.  
   * Veja a planta do piso e a localização dos transportadores robóticos dentro da estrutura de realização.
   * Acionar comandos, tais como repor o sistema de controlo, atualizar o firmware da transportadora e reconfigurar a rede.

     ![Screenshot do painel de micro-realização do Centro de Traders de Northwind](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Consulte um exemplo do painel de instrumentos que um operador pode utilizar para monitorizar as condições dentro do centro de realização. 
   * Monitorize a saúde das cargas que estão em execução no dispositivo de gateway dentro do centro de realização.    

     ![Screenshot do painel de micro-realização do Centro de Traders de Northwind](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Modelo de dispositivo
Se selecionar o separador de modelos do dispositivo, verá que existem dois tipos diferentes de dispositivos que fazem parte do modelo: 
   * **Transportadora Robótica**: Este modelo de dispositivo representa a definição para um transportador robótico funcional que foi implantado na estrutura de realização, e está a realizar operações adequadas de armazenamento e recuperação. Se selecionar o modelo, verá que o robô está a enviar dados do dispositivo, como temperatura e posição de eixo, e propriedades como o estado do portador robótico. 
   * **Monitorização da Condição da Estrutura**: Este modelo de dispositivo representa uma recolha de dispositivos que lhe permite monitorizar a condição ambiental, bem como o dispositivo gateway que acolhe várias cargas de trabalho de borda para alimentar o seu centro de realização. O dispositivo envia dados de telemetria, como a temperatura, o número de palhetas e o número de encomendas. Também envia informações sobre o estado e a saúde das cargas de trabalho computacionais em funcionamento no seu ambiente. 

     ![Modelos de dispositivo sinuoso de centro de micro-realização](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Se selecionar o separador de grupos de dispositivos, também verá que estes modelos de dispositivo saem automaticamente de grupos de dispositivos criados para os mesmos.

## <a name="rules"></a>Regras
No separador **Regras,** vê-se uma regra da amostra que existe no modelo de aplicação para monitorizar as condições de temperatura do transportador robótico. Pode utilizar esta regra para alertar o operador se um robô específico da instalação estiver a sobreaquecer e tiver de ser desligado para manutenção. 

Use a regra da amostra como inspiração para definir regras mais adequadas para as suas funções empresariais.

![Screenshot do separador Regras](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o modelo de aplicação. Vá às**definições**de Aplicação **de Administração,** > e selecione **Eliminar**.

![Screenshot da página de definições de aplicações do centro de micro-realização](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura de soluções do [centro de micro-realização.](./architecture-micro-fulfillment-center.md)
* Saiba mais sobre outros modelos de [retalho Azure IoT Central.](./overview-iot-central-retail.md)
* Leia a visão geral da [Central Azure IoT.](../preview/overview-iot-central.md)
