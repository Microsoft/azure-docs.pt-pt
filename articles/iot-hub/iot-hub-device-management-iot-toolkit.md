---
title: Gestão de dispositivos Azure IoT com ferramentas Azure IoT para VSCode
description: Utilize as Ferramentas Azure IoT para o Código de Estúdio Visual para a gestão de dispositivos Azure IoT Hub, com os métodos Direct e as opções de gestão de propriedades desejadas pela Twin.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9d4d82472664900c96b77b31740573d0463465b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911917"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Utilize ferramentas Azure IoT para código de estúdio visual para gestão de dispositivos Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[As Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão útil do Código do Estúdio Visual que facilita a gestão do IoT Hub e o desenvolvimento de aplicações IoT. Vem com opções de gestão que pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça um dispositivo agir como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Ler dispositivo gémeo           | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Atualizar dispositivo twin         | Coloque um dispositivo em certos estados, tais como colocar um LED a verde ou definir o intervalo de envio de telemetria para 30 minutos.         |
| Mensagens cloud-to-device   | Envie notificações para um dispositivo. Por exemplo: "É muito provável que chova hoje. Não se esqueça de trazer um guarda-chuva.              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte a orientação de [comunicação Dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub persiste um dispositivo gémeo para cada dispositivo que se liga ao mesmo. Para mais informações sobre gémeos dispositivos, consulte [Começar com gémeos dispositivos](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>O que irá aprender

Aprende a usar ferramentas Azure IoT para Código de Estúdio Visual com várias opções de gestão na sua máquina de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Executar Ferramentas Azure IoT para Código de Estúdio Visual com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

* Uma subscrição ativa do Azure.
* Um hub Azure IoT sob a sua assinatura.
* [Código de estúdio visual](https://code.visualstudio.com/)
* [Ferramentas Azure IoT para código VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou [abra este link no Código](vscode:extension/vsciot-vscode.azure-iot-tools)do Estúdio Visual .

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

1. Na visão do **Explorer** do Código VS, expanda a secção de **dispositivos Hub Azure IoT** no canto inferior esquerdo.

2. Clique em **Selecionar Hub IoT** no menu de contexto.

3. Um pop-up vai aparecer no canto inferior direito para deixá-lo assinar no Azure pela primeira vez.

4. Depois de iniciar sessão, será mostrada a sua lista de subscrição Azure e, em seguida, selecione A Subscrição Azure e o IoT Hub.

5. A lista de dispositivos será mostrada no separador **Dispositivos Hub Azure IoT** em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação política **iothubowner** para o hub IoT a que o seu dispositivo IoT se conecta na janela pop-up.

## <a name="direct-methods"></a>Métodos diretos

1. Clique no seu dispositivo e selecione **Invocar método direto**. 

2. Introduza o nome do método e a carga útil na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT** > **Azure IoT Hub.**

## <a name="read-device-twin"></a>Ler dispositivo gémeo

1. Clique no seu dispositivo e selecione **Editar Dispositivo Twin**. 

2. Será aberto um ficheiro **azure-iot-device-twin.json** com o conteúdo do dispositivo twin.

## <a name="update-device-twin"></a>Atualizar dispositivo twin

1. Faça algumas edificações de **tags** ou **propriedades.campo desejado.**

2. Clique à direita no ficheiro **azure-iot-device-twin.json.**

3. Selecione **Update Device Twin** para atualizar o dispositivo twin.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:
 
1. Clique no seu dispositivo e selecione **Enviar mensagem C2D para o dispositivo**. 

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT** > **Azure IoT Hub.**

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar a extensão de Ferramentas Azure IoT para Código de Estúdio Visual com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
