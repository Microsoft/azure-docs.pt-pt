---
title: Azure gestão de dispositivos de IoT com ferramentas de IoT do Azure para Visual Studio Code | Documentos da Microsoft
description: Utilize as ferramentas de IoT do Azure para Visual Studio Code para gestão de dispositivos do IoT Hub do Azure, com os métodos diretos e opções de gestão de propriedades pretendidas do duplo.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9b739fe3a8bd068d55009498b56f315b197769b1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727541"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Utilizar ferramentas de IoT do Azure para Visual Studio Code para gestão de dispositivos do IoT Hub do Azure

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/2.png)

[Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão do Visual Studio Code útil que facilita o IoT Hub gestão e desenvolvimento de aplicações de IoT. Trata-se com as opções de gestão que pode utilizar para efetuar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Tornar um dispositivo atuar como iniciar ou interromper o envio de mensagens ou reiniciar o dispositivo.                                        |
| Dispositivo duplo de leitura           | Obter o Estado comunicado de um dispositivo. Por exemplo, o dispositivo comunica que o LED é intermitente agora.                                    |
| Atualizar o dispositivo duplo         | Coloca um dispositivo em determinados Estados, como na definição de um LED verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Mensagens da cloud para dispositivo   | Envie notificações para um dispositivo. Por exemplo, "é grande probabilidade de chuva hoje mesmo. Não se esqueça de colocar uma síntese."              |

Para obter mais explicações sobre as diferenças e as orientações sobre como utilizar estas opções, consulte [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) e [orientação de comunicação do Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub cria um dispositivo duplo para cada dispositivo que se liga ao mesmo. Para obter mais informações sobre dispositivos duplos, consulte [introdução aos dispositivos duplos](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>O que irá aprender

Saiba com as ferramentas de IoT do Azure para Visual Studio Code com várias opções de gestão no computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute as ferramentas de IoT do Azure para Visual Studio Code com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

* Uma subscrição ativa do Azure.
* Um hub IoT do Azure com a sua subscrição.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Azure para o VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o seu hub IoT

1. Na **Explorer** ver do VS Code, expanda **dispositivos do Azure IoT Hub** secção no canto inferior esquerdo.

2. Clique em **selecione o IoT Hub** no menu de contexto.

3. Mostra um pop-up no canto inferior direito para permitem-lhe iniciar sessão no Azure pela primeira vez.

4. Depois de iniciar sessão, sua lista de subscrições do Azure serão apresentados, em seguida, selecione a subscrição do Azure e o IoT Hub.

5. A lista de dispositivos será mostrada na **dispositivos do Azure IoT Hub** guia em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação do hub IoT que se liga o dispositivo de IoT na janela de pop-up.

## <a name="direct-methods"></a>Métodos diretos

1. O dispositivo com o botão direito e selecione **invocar método direto**. 

2. Introduza o nome do método e o payload na caixa de entrada.

3. Os resultados serão mostrados na **saída** > **o Kit de ferramentas do Azure IoT Hub** vista.

## <a name="read-device-twin"></a>Dispositivo duplo de leitura

1. O dispositivo com o botão direito e selecione **Editar dispositivo duplo**. 

2. Uma **azure-iot-dispositivo-twin.json** será possível abrir o ficheiro com o conteúdo do dispositivo duplo.

## <a name="update-device-twin"></a>Atualizar o dispositivo duplo

1. Faça algumas edições dos **etiquetas** ou **properties.desired** campo.

2. Com o botão direito sobre a **azure-iot-dispositivo-twin.json** ficheiro.

3. Selecione **atualizar o dispositivo duplo** para atualizar o dispositivo duplo.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:
 
1. O dispositivo com o botão direito e selecione **enviar mensagem de C2D para dispositivos**. 

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na **saída** > **o Kit de ferramentas do Azure IoT Hub** vista.

## <a name="next-steps"></a>Passos Seguintes

Aprendeu como utilizar a extensão Azure IoT Tools para Visual Studio Code com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
