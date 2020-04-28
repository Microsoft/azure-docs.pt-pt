---
title: Use vs Cloud Explorer para gerir mensagens de dispositivo SoT Hub Azure IoT
description: Saiba como usar o Cloud Explorer para o Estúdio Visual para monitorizar o dispositivo para cloud messages e enviar mensagens de nuvem para dispositivos no Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74079485"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Use o Cloud Explorer para o Estúdio Visual enviar e receber mensagens entre o seu dispositivo e o IoT Hub

![Diagrama de ponta a ponta](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão útil do Estúdio Visual que lhe permite visualizar os seus recursos Azure, inspecionar as suas propriedades e realizar ações de desenvolvimento chave a partir do Visual Studio. Este artigo foca-se em como usar o Cloud Explorer para enviar e receber mensagens entre o seu dispositivo e o seu hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste artigo, aprende-se a usar o Cloud Explorer para o Visual Studio para monitorizar mensagens de dispositivo-nuvem e enviar mensagens cloud-to-device. As mensagens dispositivo-a-nuvem podem ser dados de sensores que o seu dispositivo recolhe e depois envia para o seu Hub IoT. As mensagens cloud-to-device podem ser comandos que o seu Hub IoT envia para o seu dispositivo. Por exemplo, pisque um LED ligado ao seu dispositivo.

## <a name="what-you-do"></a>O que faz

Neste artigo, você faz as seguintes tarefas:

- Utilize o Cloud Explorer para o Estúdio Visual para monitorizar as mensagens dispositivo-nuvem.

- Utilize o Cloud Explorer para o Estúdio Visual para enviar mensagens cloud-to-device.

## <a name="what-you-need"></a>Do que precisa

Precisa dos seguintes pré-requisitos:

- Uma subscrição ativa do Azure.

- Um Hub Azure IoT sob a sua subscrição.

- Microsoft Visual Studio 2017 Update 9 ou mais tarde. Este artigo utiliza o [Visual Studio 2019.](https://www.visualstudio.com/vs/)

- O componente Cloud Explorer do Instalador de Estúdio Visual, que é selecionado por padrão com a Carga de Trabalho Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Instalador de Estúdio Visual para O Estúdio Visual 2017 apenas suporta a monitorização de mensagens de dispositivos para nuvem e cloud-to-device. Para utilizar o Visual Studio 2017, descarregue e instale o mais recente [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Inscreva-se para aceder ao seu hub

Para aceder ao seu hub, siga estes passos:

1. No Estúdio Visual, selecione **Ver** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone de Gestão de Conta para mostrar as suas subscrições.

    ![Ícone de Gestão de Conta](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se estiver inscrito no Azure, as suas contas aparecem. Para assinar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as subscrições Azure que pretende utilizar e escolha **Aplicar**.

1. Expanda a sua subscrição e, em seguida, expanda **os Hubs IoT**.  Debaixo de cada centro, pode ver os seus dispositivos para aquele centro.

    ![Lista de Dispositivos](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorizar mensagens dispositivo-nuvem

Para monitorizar as mensagens enviadas do seu dispositivo para o seu Hub IoT, siga estes passos:

1. Clique à direita no seu Hub IoT ou dispositivo e **selecione Iniciar a monitorização da mensagem D2C**.

    ![Iniciar a monitorização da mensagem D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. As mensagens monitorizadas aparecem no âmbito **da Saída**.

    ![Resultado da mensagem D2C de monitorização](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Para parar de monitorizar, clique à direita em qualquer Hub IoT ou dispositivo e **selecione Stop Monitoring D2C Message**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu Hub IoT para o seu dispositivo, siga estes passos:

1. Clique no seu dispositivo e selecione **Enviar mensagem C2D**.

1. Introduza a mensagem na caixa de entrada.

    ![Enviar mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Os resultados aparecem na **saída**.

    ![Enviar resultado da mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Passos seguintes

Aprendeu a monitorizar mensagens dispositivo-nuvem e a enviar mensagens cloud-to-device entre o seu dispositivo IoT e o Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]