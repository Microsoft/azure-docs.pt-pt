---
title: 'Tutorial: criar um aplicativo de monitoramento penal solar com IoT Central'
description: 'Tutorial: saiba como criar um aplicativo de painel solar usando os modelos de aplicativo IoT Central do Azure.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 5560cb31e31f7cf33ac8ae71d649e88be3dbb6e6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112504"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: criar e percorrer o modelo de aplicativo de monitoramento do painel solar 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você pelo processo de criação do aplicativo de monitoramento de painel solar, que inclui um modelo de dispositivo de exemplo com dados simulados. Neste tutorial, ficará a saber:


> [!div class="checklist"]
> * Criar o aplicativo de painel solar gratuitamente
> * Passo a passo do aplicativo
> * Limpar recursos


Se você não tiver uma assinatura, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhum
- A assinatura do Azure é recomendada, mas não é necessária para experimentar


## <a name="create-a-solar-panel-monitoring-app"></a>Criar uma aplicação de monitorização de painéis solares 

Você pode criar esse aplicativo em três etapas simples:

1. Abra o [Azure IoT Central Home Page](https://apps.azureiotcentral.com) e clique em **Compilar** para criar um novo aplicativo. 

2. Selecione a guia **energia** e clique em **criar aplicativo** no bloco **painel solar monitoramento** do aplicativo. 

    > [!div class="mx-imgBorder"]
    > ![](media/tutorial-iot-central-solar-panel/solar-panel-build.png) de aplicativo de compilação
  
3. **Criar aplicativo** abrirá o formulário **novo aplicativo** . Preencha os detalhes solicitados, conforme mostrado na figura abaixo:
    * **Nome do aplicativo**: escolha um nome para seu aplicativo IOT central. 
    * **URL**: escolha uma url de IOT central, a plataforma verificará sua exclusividade.
    * **avaliação gratuita de 7 dias**: se você já tiver uma assinatura do Azure, a configuração padrão será recomendada. Se você não tiver uma assinatura do Azure, comece com a avaliação gratuita.
    * **Informações de cobrança**: o próprio aplicativo é gratuito. O diretório, a assinatura do Azure e os detalhes de região são necessários para provisionar os recursos para seu aplicativo.
    * Clique no botão **criar** na parte inferior da página e seu aplicativo será criado em um minuto.
        > [!div class="mx-imgBorder"]
        > ![formulário de novo aplicativo](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificar o aplicativo e os dados simulados

O aplicativo de painel solar recém-criado é seu aplicativo e você pode modificá-lo a qualquer momento. Vamos garantir que o aplicativo seja implantado e funcione conforme o esperado antes de modificá-lo.

Para verificar a criação do aplicativo e a simulação de dados, vá para o **painel**. Se você puder ver os blocos com alguns dados, a implantação do aplicativo foi bem-sucedida. A simulação de dados pode levar alguns minutos para gerar os dados, portanto, forneça 1-2 minutos. 

## <a name="application-walk-through"></a>Passo a passo do aplicativo
Depois de implantar com êxito o modelo de aplicativo, ele vem com o dispositivo de medidor inteligente de exemplo, o modelo de dispositivo e o painel.

Adatum é uma empresa de energia fictícia, que monitora e gerencia painéis solares. No painel de monitoramento do painel solar, você vê as propriedades do painel solar, os dados e os comandos de exemplo. Ele permite que os operadores e as equipes de suporte executem proativamente as seguintes atividades antes que ele se transforme em incidentes de suporte:
* Examine as informações mais recentes do painel e seu local de instalação no mapa
* Verificar proativamente o status do painel e o status da conexão
* Examine as tendências de geração de energia e temperatura para capturar quaisquer padrões anormais
* Acompanhe a geração de energia total para fins de planejamento e cobrança
* Operações de comando e controle, como ativar o painel e atualizar a versão do firmware. No modelo, os botões de comando mostram as funcionalidades possíveis e não enviam comandos reais.

> [!div class="mx-imgBorder"]
> painel de monitoramento do painel solar ![](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivos
O aplicativo vem com um dispositivo de painel solar de exemplo. Você pode ver os detalhes do dispositivo clicando na guia **dispositivos** .

> [!div class="mx-imgBorder"]
> ![dispositivos do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Clique no link **SP0123456789** do dispositivo de exemplo para ver os detalhes do dispositivo. Na página **Propriedades da atualização** , você pode atualizar as propriedades graváveis do dispositivo e visualizar os valores atualizados no painel. 

> [!div class="mx-imgBorder"]
> ![Propriedades do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modelo de dispositivo
Clique na guia **modelos de dispositivo** para ver o modelo de dispositivo do painel solar. O modelo tem a interface predefinida para dados, propriedade, comandos e exibições.

> [!div class="mx-imgBorder"]
> ![modelo de dispositivos do painel solar](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Limpar recursos
Se você decidir não continuar usando este aplicativo, exclua seu aplicativo com as seguintes etapas:

1. No painel esquerdo, abra a guia Administração
2. Selecione Configurações do aplicativo e clique no botão excluir na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![excluir](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png) de aplicativo


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura do aplicativo de painel solar consulte [o artigo conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Criar modelos de aplicativo de painel solar gratuitamente: [aplicativo de painel solar](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Saiba mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/)

