---
title: 'Tutorial: Criar uma aplicação de análise de contadores inteligentes com a IoT Central'
description: 'Tutorial: Aprenda a criar uma aplicação de monitorização de contadores inteligentes utilizando modelos de aplicação Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7b04251211e716ef8e5f8c77aa3a2e74f6e9ef77
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982512"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Criar e passar pelo modelo de aplicação de monitorização de contadores inteligentes 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial guia-o através do processo de criação da aplicação de monitorização de contadores inteligentes, que inclui um modelo de dispositivo de amostra com dados simulados. Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Crie gratuitamente a App Smart Meter
> * Candidatura walk-through
> * Limpar recursos


Se não tem uma subscrição, [crie uma conta de teste gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhuma
- A subscrição do Azure é recomendada, mas não é necessária para experimentar

## <a name="create-a-smart-meter-monitoring-app"></a>Criar uma aplicação de monitorização de medidores inteligentes 

Pode criar esta aplicação em três passos simples:

1. Abra a [página inicial do Azure IoT Central](https://apps.azureiotcentral.com) e clique em **Construir** para criar uma nova aplicação. 

2. Selecione o separador **Energia** e clique **em Criar app** sob o azulejo de **monitorização de contadores inteligentes.**

    > [!div class="mx-imgBorder"]
    > ![construir app](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **A Create app** abrirá o novo formulário de **aplicação.** Preencha os detalhes solicitados, conforme indicado no valor abaixo:
    * **Nome da candidatura**: Escolha um nome para a sua aplicação IoT Central. 
    * **URL**: Escolha um URL Central IoT, a plataforma verificará a sua singularidade.
    * Teste gratuito de 7 dias : Se já tiver uma subscrição Azure, **recomenda-se**a definição predefinida. Se não tiver uma assinatura Azure, comece com um teste gratuito.
    * **Faturação Informação**: A aplicação em si é gratuita. Os detalhes do Diretório, Azure e Região são necessários para fornecer os recursos para a sua app.
    * Clique em criar o botão **Na** parte inferior da página e a sua aplicação será criada dentro de um minuto ou mais.     
         ![novo formulário de candidatura](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

         ![Nova informação de faturação de formulário de aplicação](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificar a aplicação e simular dados

A aplicação de contadores inteligentes recém-criada é a sua aplicação e pode modificá-la a qualquer momento. Vamos garantir que a aplicação é implementada e funciona como esperado antes de a modificar.

Para verificar a criação da aplicação e simulação de dados, vá ao **Dashboard**. Se conseguir ver os azulejos com alguns dados, então a implementação da sua aplicação foi bem sucedida. A simulação de dados pode demorar alguns minutos a gerar os dados, por isso dê-lhe 1-2 minutos. 

## <a name="application-walk-through"></a>Candidatura walk-through
Depois de implementar com sucesso o modelo de aplicação, ele vem com dispositivo de contador inteligente de amostra, modelo de dispositivo e um dashboard. 

A Adatum é uma empresa de energia fictícia, que monitoriza e gere contadores inteligentes. No painel de monitorização de contadores inteligentes, você vê propriedades de contadores inteligentes, dados e comandos de amostra. Permite que os operadores e equipas de apoio realizem proactivamente as seguintes atividades antes de se transformar em incidentes de apoio: 
* Reveja a informação mais recente do medidor e a sua localização instalada no mapa
* Verifique proativamente a rede de contadores e o estado de ligação 
* Monitor Min e Max leituras de tensão para a saúde da rede 
* Reveja as tendências de energia, energia e tensão para capturar quaisquer padrões anómalos 
* Acompanhe o consumo total de energia para fins de planeamento e faturação
* Operações de comando e controlo, tais como reconectar o medidor e atualizar a versão firmware. No modelo, os botões de comando mostram as possíveis funcionalidades e não enviam comandos reais. 

> [!div class="mx-imgBorder"]
> ![](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png) de monitorização do painel de monitorização do contador Inteligente

### <a name="devices"></a>Dispositivos
A aplicação vem com um dispositivo de contador inteligente de amostras. Pode ver os detalhes do dispositivo clicando no separador **Dispositivos.**

> [!div class="mx-imgBorder"]
> ![dispositivos de contador Inteligente](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Clique no dispositivo de amostra **sM0123456789** para ver os detalhes do dispositivo. Pode atualizar as propriedades do dispositivo na página **Update Properties** e visualizar os valores atualizados no painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![propriedades do contador Inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modelo de dispositivo
Clique no separador de **modelos do Dispositivo** para ver o modelo do dispositivo de contador inteligente. O modelo tem interface pré-definido para Dados, Propriedades, Comandos e Vistas.

> [!div class="mx-imgBorder"]
> ![modelos de dispositivo de contador inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Limpar recursos
Se decidir não continuar a utilizar esta aplicação, elimine a sua aplicação com os seguintes passos:

1. Do painel esquerdo, separador de administração aberto
2. Selecione as definições de Aplicação e clique em Eliminar o botão na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![Eliminar](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png) de aplicação


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura de aplicativos smart meter consulte [o artigo de conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Crie modelos de aplicação de contadores inteligentes gratuitamente: [app smart meter](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Saiba mais sobre a IoT Central, consulte a [visão geral da IoT Central](https://docs.microsoft.com/azure/iot-central/)
