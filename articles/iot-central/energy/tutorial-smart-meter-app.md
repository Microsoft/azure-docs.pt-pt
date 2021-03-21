---
title: 'Tutorial: Criar uma app de análise de contadores inteligentes com IoT Central'
description: 'Tutorial: Saiba como criar uma aplicação de monitorização de contadores inteligentes utilizando modelos de aplicação Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d9b5af45ab9a1003cc25e8b1ea2059b83bc715c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833308"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Criar e percorrer o modelo de aplicação de monitorização do contador inteligente 

Este tutorial guia-o através do processo de criação da aplicação de monitorização do contador inteligente, que inclui um modelo de dispositivo de amostra com dados simulados. Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Crie gratuitamente a App Smart Meter
> * Walk-through de aplicação
> * Limpar os recursos


Se não tiver uma subscrição, [crie uma conta de teste gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhum
- A subscrição do Azure é recomendada, mas não é necessária para experimentar

## <a name="create-a-smart-meter-monitoring-app"></a>Criar uma aplicação de monitorização de medidores inteligentes 

Pode criar esta aplicação em três passos simples:

1. Abra [a página inicial do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Construir** para criar uma nova aplicação. 
1. Selecione o separador **Energia** e clique em **Criar app** em azulejo de aplicação de **monitorização de contadores** inteligentes.

    > [!div class="mx-imgBorder"]
    > ![Construir App](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. **A aplicação Create** abrirá o novo formulário **de aplicação.** Preencha os dados solicitados, tal como indicado na figura abaixo:
    * **Nome da aplicação**: Escolha um nome para a sua aplicação IoT Central. 
    * **URL**: Escolha um URL IoT Central, a plataforma verificará a sua singularidade.
    * **Ensaio gratuito de 7 dias**: Se já tiver uma subscrição Azure, recomenda-se a definição predefinida. Se não tiver uma assinatura Azure, comece com o teste gratuito.
    * **Billing Info**: A aplicação em si é gratuita. Os detalhes do Diretório, Azure e Região são necessários para a disponibilização dos recursos para a sua aplicação.
    * Clique em **Criar** botão na parte inferior da página e a sua aplicação será criada em um minuto ou mais.

        ![Formulário de nova aplicação](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Nova informação de faturação do formulário de candidatura](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Verifique a aplicação e os dados simulados

A aplicação de contadores inteligentes recém-criada é a sua app e pode modificá-la a qualquer momento. Vamos garantir que a aplicação está implantada e funciona como esperado antes de modificá-la.

Para verificar a criação de aplicações e simulação de dados, vá ao **Dashboard**. Se conseguir ver os azulejos com alguns dados, então a implementação da sua aplicação foi bem sucedida. A simulação de dados pode demorar alguns minutos a gerar os dados, por isso dê-lhe 1-2 minutos. 

## <a name="application-walk-through"></a>Walk-through de aplicação
Depois de implementar com sucesso o modelo de aplicação, ele vem com o dispositivo de contador inteligente de amostra, modelo de dispositivo e um dashboard. 

A Adatum é uma empresa de energia fictícia, que monitoriza e gere contadores inteligentes. No painel de monitorização do contador inteligente, vê propriedades, dados e comandos de amostras inteligentes. Permite que os operadores e equipas de apoio realizem proactivamente as seguintes atividades antes de se transformarem em incidentes de apoio: 
* Reveja as informações mais recentes do medidor e a sua localização instalada no mapa
* Verifique proativamente a rede do contador e o estado da ligação 
* Monitorize leituras de tensão Min e Max para a saúde da rede 
* Reveja as tendências de energia, energia e tensão para capturar quaisquer padrões anómalos 
* Acompanhe o consumo total de energia para fins de planeamento e faturação
* Operações de comando e controlo, tais como reencontrar o medidor e atualizar a versão do firmware. No modelo, os botões de comando mostram as possíveis funcionalidades e não enviam comandos reais. 

> [!div class="mx-imgBorder"]
> ![Painel de monitorização de contadores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Dispositivos
A aplicação vem com um dispositivo de contador inteligente de amostra. Pode ver os detalhes do dispositivo clicando no **separador Dispositivos.**

> [!div class="mx-imgBorder"]
> ![Dispositivos de contadores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Clique no link **SM0123456789** do dispositivo para ver os detalhes do dispositivo. Pode atualizar as propriedades do dispositivo na página **'Update Properties'** e visualizar os valores atualizados no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Propriedades de contadores inteligentes](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modelo de dispositivo
Clique no **separador modelos do dispositivo** para ver o modelo do dispositivo do contador inteligente. O modelo tem interface pré-definido para Dados, Propriedade, Comandos e Vistas.

> [!div class="mx-imgBorder"]
> ![Modelos de dispositivo de medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Se decidir não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Do painel esquerdo, abra o separador administração
1. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Excluir aplicação](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a arquitetura de aplicativos smart meter, consulte:

> [!div class="nextstepaction"]
> [Arquitetura de aplicação de contadores inteligentes](./concept-iot-central-smart-meter-app.md)
