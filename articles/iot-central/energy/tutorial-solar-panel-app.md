---
title: 'Tutorial: Criar uma app de monitorização de painéis solares com ioT Central'
description: 'Tutorial: Saiba como criar uma aplicação de painel solar utilizando modelos de aplicação Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c0f4c4deaa57b1414a3ef55226e4c451b53ba72c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971310"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Criar e passear através do modelo de aplicação de monitorização do painel solar 



Este tutorial guia-o através do processo de criação da aplicação de monitorização do painel solar, que inclui um modelo de dispositivo de amostra com dados simulados. Neste tutorial, ficará a saber:


> [!div class="checklist"]
> * Crie gratuitamente a aplicação do painel solar
> * Walk-through de aplicação
> * Limpar os recursos


Se não tiver uma subscrição, [crie uma conta de teste gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhum
- A subscrição do Azure é recomendada, mas não é necessária para experimentar


## <a name="create-a-solar-panel-monitoring-app"></a>Criar uma aplicação de monitorização de painéis solares 

Pode criar esta aplicação em três passos simples:

1. Abra [a página inicial do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Construir** para criar uma nova aplicação. 

2. Selecione o separador **Energia** e clique em **Criar aplicativo** sob azulejo de aplicação de **monitorização do painel solar.** 

    > [!div class="mx-imgBorder"]
    > ![Construir App](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Criar aplicativo** abrirá novo formulário **de aplicação.** Preencha os dados solicitados, tal como indicado na figura abaixo:
    * **Nome da aplicação**: Escolha um nome para a sua aplicação IoT Central. 
    * **URL**: Escolha um URL IoT Central, a plataforma verificará a sua singularidade.
    * **Ensaio gratuito de 7 dias**: Se já tiver uma subscrição Azure, recomenda-se a definição predefinida. Se não tiver uma assinatura Azure, comece com o teste gratuito.
    * **Billing Info**: A aplicação em si é gratuita. Os detalhes do Diretório, Azure e Região são necessários para a disponibilização dos recursos para a sua aplicação.
    * Clique em **Criar** botão na parte inferior da página e a sua aplicação será criada em um minuto ou mais.
        ![Formulário de nova aplicação](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Nova informação de faturação do formulário de candidatura](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifique a aplicação e os dados simulados

A nova aplicação de painel solar criada é a sua app e pode modificá-la a qualquer momento. Vamos garantir que a aplicação está implantada e funciona como esperado antes de modificá-la.

Para verificar a criação de aplicações e simulação de dados, vá ao **Dashboard**. Se conseguir ver os azulejos com alguns dados, então a implementação da sua aplicação foi bem sucedida. A simulação de dados pode demorar alguns minutos a gerar os dados, por isso dê-lhe 1-2 minutos. 

## <a name="application-walk-through"></a>Walk-through de aplicação
Depois de implementar com sucesso o modelo de aplicação, ele vem com o dispositivo de contador inteligente de amostra, modelo de dispositivo e painel de instrumentos.

A Adatum é uma empresa de energia fictícia, que monitoriza e gere painéis solares. No painel de monitorização do painel solar, vê-se propriedades, dados e comandos de amostras de painéis solares. Permite que os operadores e equipas de apoio realizem proactivamente as seguintes atividades antes de se transformarem em incidentes de apoio:
* Reveja as informações mais recentes do painel e a sua localização instalada no mapa
* Verifique proativamente o estado do painel e o estado da ligação
* Reveja as tendências de geração de energia e temperatura para capturar quaisquer padrões anómalos
* Acompanhe a geração total de energia para fins de planeamento e faturação
* Operações de comando e controlo, tais como ativar o painel e atualizar a versão do firmware. No modelo, os botões de comando mostram as possíveis funcionalidades e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> ![Painel de monitorização do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
A aplicação vem com um dispositivo de painel solar de amostra. Pode ver os detalhes do dispositivo clicando no **separador Dispositivos.**

> [!div class="mx-imgBorder"]
> ![Dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Clique no link DO dispositivo de amostra **SP0123456789** para ver os detalhes do dispositivo. Na página **'Update Properties',** pode atualizar as propriedades do dispositivo e visualizar os valores atualizados no painel de instrumentos. 

> [!div class="mx-imgBorder"]
> ![Propriedades do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Clique no **separador modelos do dispositivo** para ver o modelo do dispositivo do painel solar. O modelo tem interface pré-definido para Dados, Propriedade, Comandos e Vistas.

> [!div class="mx-imgBorder"]
> ![Modelo de dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Se decidir não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Do painel esquerdo, abra o separador administração
2. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Excluir aplicação](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura de aplicativos de painel solar refere-se a 
> [!div class="nextstepaction"]
> [o artigo conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Crie modelos de aplicação de painel solar gratuitamente: [aplicativo painel solar](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Saiba mais sobre o IoT Central, consulte [a visão geral do IoT Central](https://docs.microsoft.com/azure/iot-central/)