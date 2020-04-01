---
title: 'Tutorial: Criar uma app de monitorização penal solar com a IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de painel solar utilizando modelos de aplicação Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025780"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Criar e passar pelo modelo de aplicação de monitorização do painel solar 



Este tutorial guia-o através do processo de criação da aplicação de monitorização do painel solar, que inclui um modelo de dispositivo de amostra com dados simulados. Neste tutorial, ficará a saber:


> [!div class="checklist"]
> * Crie gratuitamente a aplicação do painel solar
> * Candidatura walk-through
> * Limpar recursos


Se não tem uma subscrição, [crie uma conta de teste gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhuma
- A subscrição do Azure é recomendada, mas não é necessária para experimentar


## <a name="create-a-solar-panel-monitoring-app"></a>Criar uma aplicação de monitorização de painéis solares 

Pode criar esta aplicação em três passos simples:

1. Abra a [página inicial do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Construir** para criar uma nova aplicação. 

2. Selecione o separador **Energia** e clique **em Criar aplicação** sob o azulejo de monitorização de **painéis solares.** 

    > [!div class="mx-imgBorder"]
    > ![Construir App](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Criar app** abrirá novo formulário de **aplicação.** Preencha os detalhes solicitados, conforme indicado no valor abaixo:
    * **Nome da candidatura**: Escolha um nome para a sua aplicação IoT Central. 
    * **URL**: Escolha um URL Central IoT, a plataforma verificará a sua singularidade.
    * Teste gratuito de 7 dias : Se já tiver uma subscrição Azure, **recomenda-se**a definição predefinida. Se não tiver uma assinatura Azure, comece com um teste gratuito.
    * **Faturação Informação**: A aplicação em si é gratuita. Os detalhes do Diretório, Azure e Região são necessários para fornecer os recursos para a sua app.
    * Clique em criar o botão **Na** parte inferior da página e a sua aplicação será criada dentro de um minuto ou mais.
        ![Novo formulário de candidatura](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Nova informação de faturação de formulário de aplicação](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificar a aplicação e simular dados

A aplicação de painel solar recém-criada é a sua app e pode modificá-la a qualquer momento. Vamos garantir que a aplicação é implementada e funciona como esperado antes de a modificar.

Para verificar a criação da aplicação e simulação de dados, vá ao **Dashboard**. Se conseguir ver os azulejos com alguns dados, então a implementação da sua aplicação foi bem sucedida. A simulação de dados pode demorar alguns minutos a gerar os dados, por isso dê-lhe 1-2 minutos. 

## <a name="application-walk-through"></a>Candidatura walk-through
Depois de implementar com sucesso o modelo de aplicação, ele vem com dispositivo de contador inteligente de amostra, modelo de dispositivo e painel de instrumentos.

A Adatum é uma empresa de energia fictícia, que monitoriza e gere painéis solares. No painel de monitorização do painel solar, você vê propriedades de painéis solares, dados e comandos de amostra. Permite que os operadores e equipas de apoio realizem proactivamente as seguintes atividades antes de se transformar em incidentes de apoio:
* Reveja as últimas informações do painel e a sua localização instalada no mapa
* Verifique proativamente o estado do painel e o estado de ligação
* Reveja as tendências de geração de energia e temperatura para capturar quaisquer padrões anómalos
* Acompanhe a geração total de energia para fins de planeamento e faturação
* Operações de comando e controlo, tais como ativar a versão do painel e da atualização do firmware. No modelo, os botões de comando mostram as possíveis funcionalidades e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> ![Painel de monitorização de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
A aplicação vem com um dispositivo de painel solar de amostra. Pode ver os detalhes do dispositivo clicando no separador **Dispositivos.**

> [!div class="mx-imgBorder"]
> ![Dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Clique no dispositivo de amostra **SP0123456789** para ver os detalhes do dispositivo. Na página **Update Properties,** pode atualizar as propriedades do dispositivo e visualizar os valores atualizados no painel de instrumentos. 

> [!div class="mx-imgBorder"]
> ![Propriedades de painéis solares](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Clique no separador de **modelos do dispositivo** para ver o modelo do dispositivo do painel solar. O modelo tem interface pré-definido para Dados, Propriedades, Comandos e Vistas.

> [!div class="mx-imgBorder"]
> ![Modelo de dispositivos de painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar recursos
Se decidir não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Do painel esquerdo, separador de administração aberto
2. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Excluir aplicação](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura de aplicativos de painel solar consulte [o artigo de conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Crie modelos de aplicação de painéis solares gratuitamente: [app de painel solar](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Saiba mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/)

