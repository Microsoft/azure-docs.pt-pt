---
title: Gerir o dispositivo do IoT Hub do Azure na cloud de mensagens com o Cloud Explorer para Visual Studio | Documentos da Microsoft
description: Saiba como utilizar o Cloud Explorer para Visual Studio para monitorizar o dispositivo para mensagens na cloud e enviar na cloud para mensagens de dispositivos no IoT Hub do Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440749"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Utilizar o Cloud Explorer para Visual Studio para enviar e receber mensagens entre o seu dispositivo e o IoT Hub

![Diagrama de ponto a ponto](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão do Visual Studio útil que permite-lhe ver os recursos do Azure, Inspecione as respetivas propriedades e executar ações de chave do desenvolvedor do Visual Studio. Este artigo se concentra em como utilizar o Cloud Explorer para enviar e receber mensagens entre o seu dispositivo e o seu IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>O que aprenderá

Aprenderá a utilizar o Cloud Explorer para Visual Studio para monitorizar mensagens do dispositivo para a cloud e para enviar mensagens da cloud para o dispositivo. Mensagens do dispositivo para a cloud podem ser dados de sensor que o dispositivo recolhe e, em seguida, envia ao seu IoT Hub. Mensagens cloud para o dispositivo podem ser comandos que o seu IoT Hub envia para o seu dispositivo. Por exemplo, blink um LED que está ligada ao seu dispositivo.

## <a name="what-you-will-do"></a>O que fará

- Utilize o Cloud Explorer para Visual Studio para monitorizar mensagens do dispositivo para a cloud.
- Utilize o Cloud Explorer para Visual Studio para enviar mensagens da cloud para o dispositivo.

## <a name="what-you-need"></a>Do que precisa

- Uma subscrição ativa do Azure.
- Um IoT Hub do Azure com a sua subscrição.
- Microsoft Visual Studio 2017 Update 8 ou posterior
- Componente do cloud Explorer de instalação do Visual Studio (que é selecionado por predefinição com carga de trabalho do Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente de Cloud Explorer do Visual Studio Installer apenas suporta a monitorização de mensagens dispositivo-para-cloud e cloud-para-dispositivo. Para enviar mensagens para o dispositivo ou na cloud, transferir e instalar a versão mais recente [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu IoT Hub

1. No Visual Studio **Cloud Explorer** janela, clique no ícone de gestão de contas. É possível abrir a janela de Cloud Explorer de **View** > **Cloud Explorer** menu.

    ![Clique em gestão de contas](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Clique em **gerir contas** no Cloud Explorer.

3. Clique em **adicionar uma conta...**  na janela de novo para iniciar sessão no Azure pela primeira vez.

4. Depois de iniciar sessão, será apresentada a lista de subscrição do Azure. Selecione as subscrições do Azure que pretende visualizar e clique em **aplicar**.

5. Expanda **sua assinatura** > **IoT Hubs** > **Your IoT Hub**, será apresentada a lista de dispositivos no seu nó do IoT Hub.

    ![Lista de dispositivos](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorizar mensagens do dispositivo para a cloud

Para monitorizar mensagens que são enviadas a partir do seu dispositivo ao seu IoT Hub, siga estes passos:

1. Clique no seu dispositivo ou o IoT Hub e selecione **iniciar mensagens D2C monitorização**.

    ![Comece a monitorizar mensagens D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. As mensagens monitorizadas serão mostradas na **IoT Hub** painel de resultados.

    ![Monitoring D2C Message resultado](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Para parar a monitorização, clique com o botão direito em qualquer dispositivo ou o IoT Hub e selecione **parar mensagens D2C monitorização**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu IoT Hub para o seu dispositivo, siga estes passos:

1. O dispositivo com o botão direito e selecione **enviar mensagem de C2D**.

    ![Enviar mensagem de C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na **IoT Hub** painel de resultados.

    ![Enviar o resultado de mensagem C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Passos Seguintes

Já aprendeu a monitorizar mensagens do dispositivo para a cloud e enviar mensagens de cloud-para-dispositivo entre o seu dispositivo IoT e IoT Hub do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]