---
title: Gerenciamento de dispositivos do Azure IoT com o Cloud Explorer para Visual Studio | Microsoft Docs
description: Use o Cloud Explorer para Visual Studio para o gerenciamento de dispositivos do Hub IoT do Azure, apresentando os métodos diretos e as opções de gerenciamento de propriedades desejadas do entrelaçamento.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: e05ba421a4535e6e424e65a1f2271d19f9d9abf4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048721"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Usar o Cloud Explorer para Visual Studio para gerenciamento de dispositivos do Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

O [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão útil do Visual Studio que permite exibir os recursos do Azure, inspecionar suas propriedades e executar ações de desenvolvedor de chave no Visual Studio. Ele vem com opções de gerenciamento que você pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça um dispositivo funcionar como iniciar ou parar de enviar mensagens ou reinicializar o dispositivo.                                        |
| Ler o dispositivo           | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando agora.                                    |
| Atualizar o dispositivo de atualização         | Coloque um dispositivo em determinados Estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da cloud para dispositivo   | Enviar notificações para um dispositivo. Por exemplo, "é muito provável que haja chuva hoje em dia. Não se esqueça de trazer uma proteção. "              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre como usar essas opções, consulte [diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e diretrizes de comunicação da [nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Dispositivos gêmeos são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT persiste um dispositivo "r" para cada dispositivo que se conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte Introdução [ao dispositivo gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Neste artigo, você aprenderá a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que você faz

Neste artigo, execute o Cloud Explorer para Visual Studio com várias opções de gerenciamento.

## <a name="what-you-need"></a>Do que precisa

Você precisa dos seguintes pré-requisitos:

- Uma subscrição ativa do Azure.

- Um hub IoT do Azure em sua assinatura.

- Microsoft Visual Studio 2017 atualização 9 ou posterior. Este artigo usa o [visual studio 2017 ou o visual studio 2019](https://www.visualstudio.com/vs/).

- Componente do Cloud Explorer de Instalador do Visual Studio, que é selecionado por padrão com a carga de trabalho do Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente Cloud Explorer da Instalador do Visual Studio para Visual Studio 2017 dá suporte apenas ao monitoramento de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo. Para usar o Visual Studio 2017, baixe e instale o [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)mais recente.

## <a name="sign-in-to-access-your-hub"></a>Entre para acessar seu hub

1. No Visual Studio, selecione **Exibir** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone gerenciamento de contas para mostrar suas assinaturas.

    ![Ícone de gerenciamento de conta](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se você estiver conectado ao Azure, suas contas serão exibidas. Para entrar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as assinaturas do Azure que você deseja usar e escolha **aplicar**.

1. Expanda sua assinatura e expanda os **hubs IOT**.  Em cada Hub, você pode ver seus dispositivos para esse Hub. Clique com o botão direito do mouse em um dispositivo para acessar as opções de gerenciamento.

    ![Opções de gerenciamento](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Métodos diretos

Para usar métodos diretos, execute as seguintes etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **invocar método direto do dispositivo**.

1. Insira o nome do método e a carga em invocar o **método direto**e, em seguida, selecione **OK**.

    Os resultados aparecem na **saída**.

## <a name="update-device-twin"></a>Atualizar o dispositivo de atualização

Para editar um dispositivo, execute as seguintes etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Editar dispositivo**.

   Um arquivo **Azure-IOT-Device-entrelaça. JSON** é aberto com o conteúdo do dispositivo.

1. Faça algumas edições de **marcas** ou **Propriedades.** os campos desejados para o arquivo **Azure-IOT-Device-entrelaça. JSON** .

1. Pressione **Ctrl + S** para atualizar o dispositivo.

   Os resultados aparecem na **saída**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do Hub IoT para seu dispositivo, siga estas etapas:

1. Clique com o botão direito do mouse no dispositivo e selecione **Enviar mensagem C2D**.

1. Insira a mensagem na **mensagem enviar C2D** e selecione **OK**.

   Os resultados aparecem na **saída**.

## <a name="next-steps"></a>Passos Seguintes

Você aprendeu a usar o Cloud Explorer para Visual Studio com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
