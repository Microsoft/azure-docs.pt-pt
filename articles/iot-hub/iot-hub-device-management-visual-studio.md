---
title: Azure gestão de dispositivos de IoT com o Cloud Explorer do Visual Studio | Documentos da Microsoft
description: Utilize o Cloud Explorer para Visual Studio para a gestão de dispositivos do IoT Hub do Azure, com os métodos diretos e opções de gestão de propriedades pretendidas do duplo.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791935"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Utilizar o Cloud Explorer para Visual Studio para gestão de dispositivos do IoT Hub do Azure

![Diagrama de ponto a ponto](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão do Visual Studio útil que permite-lhe ver os recursos do Azure, Inspecione as respetivas propriedades e executar ações de chave do desenvolvedor do Visual Studio. Trata-se com as opções de gestão que pode utilizar para efetuar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Tornar um dispositivo atuar como iniciar ou interromper o envio de mensagens ou reiniciar o dispositivo.                                        |
| Dispositivo duplo de leitura           | Obter o Estado comunicado de um dispositivo. Por exemplo, o dispositivo comunica que o LED é intermitente agora.                                    |
| Atualizar o dispositivo duplo         | Coloca um dispositivo em determinados Estados, como na definição de um LED verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da cloud para dispositivo   | Envie notificações para um dispositivo. Por exemplo, "é grande probabilidade de chuva hoje mesmo. Não se esqueça de colocar uma síntese."              |

Para obter mais explicações sobre as diferenças e as orientações sobre como utilizar estas opções, consulte [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) e [orientação de comunicação do Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub cria um dispositivo duplo para cada dispositivo que se liga ao mesmo. Para obter mais informações sobre dispositivos duplos, consulte [introdução aos dispositivos duplos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Saiba como utilizar o Cloud Explorer para Visual Studio com várias opções de gestão no computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute o Cloud Explorer para Visual Studio com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

- Uma subscrição do Azure Active Directory
- Um IoT Hub do Azure com a sua subscrição
- Microsoft Visual Studio 2017 Update 8 ou posterior
- Componente do cloud Explorer de instalação do Visual Studio (que é selecionado por predefinição com carga de trabalho do Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente de Cloud Explorer do Visual Studio Installer apenas suporta a monitorização de mensagens dispositivo-para-cloud e cloud-para-dispositivo. Terá de transferir e instalar a versão mais recente [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) para aceder às opções de gestão.

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu IoT Hub

1. No Visual Studio **Cloud Explorer** janela, clique no ícone de gestão de contas. É possível abrir a janela de Cloud Explorer de **View** > **Cloud Explorer** menu.

    ![Clique em gestão de contas](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Clique em **gerir contas** no Cloud Explorer.
1. Clique em **adicionar uma conta...**  na janela de novo para iniciar sessão no Azure pela primeira vez.
1. Depois de iniciar sessão, será apresentada a lista de subscrição do Azure. Selecione as subscrições do Azure que pretende visualizar e clique em **aplicar**.
1. Expanda **sua assinatura** > **IoT Hubs** > **Your IoT Hub**, será apresentada a lista de dispositivos no seu nó do IoT Hub. Com o botão direito um dispositivo para aceder às opções de gestão.

    ![Opções de gestão](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Métodos diretos

1. O dispositivo com o botão direito e selecione **invocar método direto de dispositivo**.
1. Introduza o nome do método e o payload na caixa de entrada.
1. Os resultados serão mostrados na **IoT Hub** painel de resultados.

## <a name="read-device-twin"></a>Dispositivo duplo de leitura

1. O dispositivo com o botão direito e selecione **Editar dispositivo duplo**.
1. Uma **azure-iot-dispositivo-twin.json** será possível abrir o ficheiro com o conteúdo do dispositivo duplo.

## <a name="update-device-twin"></a>Atualizar o dispositivo duplo

1. Faça algumas edições dos **etiquetas** ou **properties.desired** campo para o **azure-iot-dispositivo-twin.json** ficheiro.
1. Prima **Ctrl + S** para atualizar o dispositivo duplo.
1. Os resultados serão mostrados na **IoT Hub** painel de resultados.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu IoT Hub para o seu dispositivo, siga estes passos:

1. O dispositivo com o botão direito e selecione **enviar mensagem de C2D**.
1. Introduza a mensagem na caixa de entrada.
1. Os resultados serão mostrados na **IoT Hub** painel de resultados.

## <a name="next-steps"></a>Passos Seguintes

Aprendeu a utilizar o Cloud Explorer para Visual Studio com diversas opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]