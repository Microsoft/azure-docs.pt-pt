---
title: Criar um aplicativo de análise de medidor inteligente com IoT Central | Microsoft Docs
description: Saiba como criar um aplicativo de monitoramento do medidor inteligente usando os modelos de aplicativo IoT Central do Azure.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 06b4fe3b716f2252f064a5b36fcde7dd7198db2d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026747"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: criar e percorrer o modelo de aplicativo de monitoramento do medidor inteligente 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial orienta você pelo processo de criação do aplicativo de monitoramento do medidor inteligente, que inclui um modelo de dispositivo de exemplo com dados simulados. Neste tutorial, ficará a saber:

> [!div class="checklist"]
> * Criar o aplicativo de medidor inteligente gratuitamente
> * Passo a passo do aplicativo
> * Limpar recursos


Se você não tiver uma assinatura, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Pré-requisitos
- Nenhuma
- A assinatura do Azure é recomendada, mas não é necessária para experimentar

## <a name="create-a-smart-meter-monitoring-app"></a>Criar um aplicativo de monitoramento do medidor inteligente 

Você pode criar esse aplicativo em três etapas simples:

1. Abra o [Azure IoT Central Home Page](https://apps.azureiotcentral.com) e clique em **Compilar** para criar um novo aplicativo. 

2. Selecione a guia **energia** e clique em **criar aplicativo** no bloco aplicativo de **monitoramento do medidor inteligente** .

    [!div class="mx-imgBorder"]
    ![](media/tutorial-iot-central-smart-meter/smart-meter-build.png) de aplicativo de compilação
    

3. **Criar aplicativo** abrirá o formulário **novo aplicativo** . Preencha os detalhes solicitados, conforme mostrado na figura abaixo:
    * **Nome do aplicativo**: escolha um nome para seu aplicativo IOT central. 
    * **URL**: escolha uma url de IOT central, a plataforma verificará sua exclusividade.
    * **avaliação gratuita de 7 dias**: se você já tiver uma assinatura do Azure, a configuração padrão será recomendada. Se você não tiver uma assinatura do Azure, comece com a avaliação gratuita.
    * **Informações de cobrança**: o próprio aplicativo é gratuito. O diretório, a assinatura do Azure e os detalhes de região são necessários para provisionar os recursos para seu aplicativo.
    * Clique no botão **criar** na parte inferior da página e seu aplicativo será criado em um minuto.     
        > [!div class="mx-imgBorder"]
        > ![formulário de novo aplicativo](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificar o aplicativo e os dados simulados

O aplicativo de medidor inteligente recém-criado é seu aplicativo e você pode modificá-lo a qualquer momento. Vamos garantir que o aplicativo seja implantado e funcione conforme o esperado antes de modificá-lo.

Para verificar a criação do aplicativo e a simulação de dados, vá para o **painel**. Se você puder ver os blocos com alguns dados, a implantação do aplicativo foi bem-sucedida. A simulação de dados pode levar alguns minutos para gerar os dados, portanto, forneça 1-2 minutos. 

## <a name="application-walk-through"></a>Passo a passo do aplicativo
Depois de implantar com êxito o modelo de aplicativo, ele vem com o dispositivo de medidor inteligente de exemplo, o modelo de dispositivo e um painel. 

Adatum é uma empresa de energia fictícia, que monitora e gerencia medidores inteligentes. No painel Monitoramento do medidor inteligente, você vê as propriedades do medidor inteligente, dados e comandos de exemplo. Ele permite que os operadores e as equipes de suporte executem proativamente as seguintes atividades antes que ele se transforme em incidentes de suporte: 
* Examine as informações de medidor mais recentes e seu local de instalação no mapa
* Verificar proativamente a rede do medidor e o status da conexão 
* Monitorar leituras mínimas e máximas de tensão para a integridade da rede 
* Examine as tendências de energia, energia e voltagem para capturar quaisquer padrões anormais 
* Acompanhar o consumo de energia total para fins de planejamento e cobrança
* Operações de comando e controle, como reconectar medidor e atualizar versão do firmware. No modelo, os botões de comando mostram as funcionalidades possíveis e não enviam comandos reais. 

[!div class="mx-imgBorder"]
![painel de monitoramento do medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Dispositivos
O aplicativo vem com um dispositivo de medidor inteligente de exemplo. Você pode ver os detalhes do dispositivo clicando na guia **dispositivos** .

[!div class="mx-imgBorder"]
![dispositivos de medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Clique no link **SM0123456789** do dispositivo de exemplo para ver os detalhes do dispositivo. Você pode atualizar as propriedades graváveis do dispositivo na página **Propriedades da atualização** e visualizar os valores atualizados no painel.

[!div class="mx-imgBorder"]
![Propriedades de medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modelo de dispositivo
Clique na guia **modelos de dispositivo** para ver o modelo de dispositivo do medidor inteligente. O modelo tem a interface predefinida para dados, propriedade, comandos e exibições.

[!div class="mx-imgBorder"]
![modelos de dispositivo do medidor inteligente](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Limpar recursos
Se você decidir não continuar usando este aplicativo, exclua seu aplicativo com as seguintes etapas:

1. No painel esquerdo, abra a guia Administração
2. Selecione Configurações do aplicativo e clique no botão excluir na parte inferior da página. 

    > [!div class="mx-imgBorder"]
    > ![excluir](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png) de aplicativo


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a arquitetura do aplicativo de medidor inteligente consulte [o artigo conceito](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Crie modelos de aplicativo de medidor inteligente gratuitamente: [aplicativo de medidor inteligente](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Saiba mais sobre IoT Central, consulte [IOT central visão geral](https://docs.microsoft.com/azure/iot-central/)
