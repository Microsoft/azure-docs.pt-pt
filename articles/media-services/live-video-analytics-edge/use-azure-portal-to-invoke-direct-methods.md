---
title: Como usar o portal Azure para invocar métodos diretos
description: Este artigo é uma visão geral usando o portal Azure para invocar métodos diretos.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279292"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Como usar o portal Azure para invocar métodos diretos

O IoT Hub dá-lhe a capacidade de invocar [métodos diretos](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) em dispositivos de borda a partir da nuvem. O módulo Live Video Analytics on IoT Edge (LVA) expõe vários [métodos diretos](/azure/media-services/live-video-analytics-edge/direct-methods) que podem ser usados para definir, implementar e instantaneamente diferentes fluxos de trabalho para analisar vídeos ao vivo.

Neste artigo, você aprenderá a invocar chamadas de método direto em Live Video Analytics para um módulo IoT Edge através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Tem o módulo Live Video Analytics no IoT Edge a funcionar no seu dispositivo de borda, utilizando os métodos descritos em [Quickstart: Live Video Analytics no IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) ou utilizando o [portal.](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device)

* Você entende [live video analytics](/azure/media-services/live-video-analytics-edge/overview) e o conceito de gráfico de [mídia](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Invocando métodos diretos através do portal Azure

Cada um dos [métodos diretos](/azure/media-services/live-video-analytics-edge/direct-methods) expostos pelo módulo LVA pode ser invocado através do portal Azure. Os passos abaixo fornecem os detalhes para um método direto. Pode invocar outros métodos diretos utilizando passos semelhantes. No entanto, cada método direto requer um corpo JSON específico.

Utilize a `GraphTopologyList` chamada de método para recuperar uma lista de todas as topologias de gráficos atualmente implantadas no módulo Live Video Analytics no módulo IoT Edge. Utilize os seguintes passos para invocar este método direto:

1. Inicie sessão no portal Azure
1. Encontre o grupo de recursos relevante a partir da sua página inicial do portal para localizar o seu Hub IoT, ou se conhece o IoT Hub, selecione-o.
    ![grupo de recursos na página inicial do portal](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Uma vez na página IoT Hub, selecione IoT Edge em Gestão Automática de Dispositivos para listar os vários IDs do dispositivo. Selecione o ID do dispositivo relevante para listar os módulos em execução no dispositivo.
    ![página de hub iot](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Selecione o live video analytics no módulo IoT Edge para apresentar a sua página de configuração.<br><br>
    ![Selecione o Live Video Analytics no módulo IoT Edge para trazer a sua página de configuração](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Selecione na opção menu de método direto. <br><br>
    ![Clique na opção de menu de método direto](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Pode ser necessário adicionar um valor nas secções de cadeia de Ligação, como pode ver na página atual. Não é necessário esconder ou alterar nada na secção de nomes de definição. Não faz mal que seja público.

1. *DigiteTopologia de Gráfico* no campo **Nome do Método.**
1. Copiar e colar o JSON abaixo no campo **De carga útil.**
    ```json
    {
    "@apiVersion":
    }
    ```
1. Selecione o botão **Método invocar** no topo da página.<br><br>
    ![invocar botão método](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Deve ver uma mensagem de estado 200 na área **de Resultados.**<br><br>
    ![tempo limite de ligação](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Respostas

| Condição             | Código de Estado | Código de Erro Detalhado |
|-----------------------|-------------|---------------------|
| Success               | 200         | N/D                 |
| Erros gerais do utilizador   | Gama 400   |                     |
| Erros gerais do servidor | Gama 500   |                     |

## <a name="next-steps"></a>Passos seguintes

Métodos mais diretos podem ser encontrados na página [de métodos diretos.](/azure/media-services/live-video-analytics-edge/direct-methods)

> [!NOTE]
> Uma instância de gráfico instantaneamente uma topologia específica, por isso, certifique-se de que tem o conjunto de topologia certo antes de criar uma instância de gráfico.

[Quickstart: Detect motion eite eventos](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) é uma boa referência para entender a sequência exata de chamadas de métodos diretos a serem feitas.
