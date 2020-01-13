---
title: Gerenciamento de dispositivo IoT do Azure com as ferramentas de IoT do Azure para VSCode
description: Use as ferramentas de IoT do Azure para Visual Studio Code para o gerenciamento de dispositivos do Hub IoT do Azure, incluindo os métodos diretos e as opções de gerenciamento de propriedades desejadas do entrelaçamento.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9d4d82472664900c96b77b31740573d0463465b8
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911917"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Usar as ferramentas do Azure IoT para Visual Studio Code para o gerenciamento de dispositivos do Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

As [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão útil Visual Studio Code que facilita o gerenciamento do Hub IOT e o desenvolvimento de aplicativos IOT. Ele vem com opções de gerenciamento que você pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça um dispositivo funcionar como iniciar ou parar de enviar mensagens ou reinicializar o dispositivo.                                        |
| Ler o dispositivo           | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando agora.                                    |
| Atualizar o dispositivo de atualização         | Coloque um dispositivo em determinados Estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da cloud para dispositivo   | Enviar notificações para um dispositivo. Por exemplo, "é muito provável que haja chuva hoje em dia. Não se esqueça de trazer uma proteção. "              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre como usar essas opções, consulte [diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e diretrizes de comunicação da [nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O Hub IoT persiste um dispositivo "r" para cada dispositivo que se conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte Introdução [ao dispositivo gêmeos](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a usar as ferramentas do Azure IoT para Visual Studio Code com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que você faz

Execute as ferramentas de IoT do Azure para Visual Studio Code com várias opções de gerenciamento.

## <a name="what-you-need"></a>Do que precisa

* Uma subscrição ativa do Azure.
* Um hub IoT do Azure em sua assinatura.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas do Azure IOT para vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou [abrir este link no Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

1. Na exibição do **Gerenciador** de vs Code, expanda a seção **dispositivos do Hub IOT do Azure** no canto inferior esquerdo.

2. Clique em **selecionar Hub IOT** no menu de contexto.

3. Um pop-up será exibido no canto inferior direito para permitir que você entre no Azure pela primeira vez.

4. Depois de entrar, sua lista de assinaturas do Azure será mostrada e, em seguida, selecione assinatura do Azure e Hub IoT.

5. A lista de dispositivos será mostrada na guia **dispositivos do Hub IOT do Azure** em alguns segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Insira a cadeia de conexão da política **iothubowner** para o Hub IOT ao qual seu dispositivo IOT se conecta na janela pop-up.

## <a name="direct-methods"></a>Métodos diretos

1. Clique com o botão direito do mouse no dispositivo e selecione **invocar método direto**. 

2. Insira o nome do método e a carga na caixa de entrada.

3. Os resultados serão mostrados na **saída** > exibição **do Hub IOT do Azure** .

## <a name="read-device-twin"></a>Ler o dispositivo

1. Clique com o botão direito do mouse no dispositivo e selecione **Editar dispositivo**. 

2. Um arquivo **Azure-IOT-Device-entrelaçado. JSON** será aberto com o conteúdo do dispositivo.

## <a name="update-device-twin"></a>Atualizar o dispositivo de atualização

1. Faça algumas edições de **marcas** ou **Propriedades. campo desejado** .

2. Clique com o botão direito do mouse no arquivo **Azure-IOT-Device-entrelaça. JSON** .

3. Selecione **Atualizar dispositivo de atualização** para atualizar o dispositivo.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do Hub IoT para seu dispositivo, siga estas etapas:
 
1. Clique com o botão direito do mouse no dispositivo e selecione **Enviar mensagem C2D para o dispositivo**. 

2. Insira a mensagem na caixa de entrada.

3. Os resultados serão mostrados na **saída** > exibição **do Hub IOT do Azure** .

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a usar a extensão de ferramentas de IoT do Azure para Visual Studio Code com várias opções de gerenciamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
