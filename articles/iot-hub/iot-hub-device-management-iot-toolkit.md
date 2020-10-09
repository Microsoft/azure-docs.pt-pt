---
title: Gestão de dispositivos Azure IoT com ferramentas Azure IoT para VSCode
description: Utilize as Ferramentas Azure IoT para Código de Estúdio Visual para gestão de dispositivos Azure IoT Hub, apresentando os métodos Diretos e as opções de gestão de propriedades desejadas pela Twin.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: d85e0e967dd802a77ccbc11b884d7a9f2891524d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81688102"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Utilize ferramentas Azure IoT para código de estúdio visual para gestão de dispositivos Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão útil do Código do Estúdio Visual que facilita a gestão do IoT Hub e o desenvolvimento de aplicações IoT. Ele vem com opções de gestão que você pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça com que um dispositivo aja como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Ler o dispositivo gémeo           | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Atualização do dispositivo twin         | Coloque um dispositivo em determinados estados, tais como a definição de um LED para verde ou a definição do intervalo de envio de telemetria para 30 minutos.         |
| Mensagens nuvem-para-dispositivo   | Envie notificações para um dispositivo. Por exemplo: "É muito provável que chova hoje. Não se esqueça de trazer um guarda-chuva.              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte [a orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação nuvem-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub persiste num dispositivo gémeo para cada dispositivo que se conecta ao mesmo. Para obter mais informações sobre os gémeos do dispositivo, consulte [Começar com os gémeos do dispositivo](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>O que irá aprender

Aprende a usar ferramentas Azure IoT para Código de Estúdio Visual com várias opções de gestão na sua máquina de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Executar ferramentas IoT Azure para Código de Estúdio Visual com várias opções de gestão.

## <a name="what-you-need"></a>O que precisa

* Uma subscrição ativa do Azure.
* Um hub Azure IoT sob a sua assinatura.
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas Azure IoT para código VS](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ou copie este URL e cole-o numa janela do navegador: `vscode:extension/vsciot-vscode.azure-iot-tools` .

## <a name="sign-in-to-access-your-iot-hub"></a>Inscreva-se para aceder ao seu hub IoT

1. Na visão do **Explorer** do Código VS, expanda a secção **de Dispositivos hub Azure IoT** no canto inferior esquerdo.

2. Clique **em Select IoT Hub** no menu de contexto.

3. Um pop-up aparecerá no canto inferior direito para que se inscreva no Azure pela primeira vez.

4. Depois de iniciar sposição, a sua lista de subscrições Azure será apresentada e, em seguida, selecione Azure Subscription e IoT Hub.

5. A lista de dispositivos será mostrada no separador **Azure IoT Hub Devices** em poucos segundos.

   > [!Note]
   > Também pode concluir a configuração ao escolher **Definir Cadeia de Ligação do Hub IoT**. Introduza a cadeia de ligação de **iothubowner** para o hub IoT a que o seu dispositivo IoT se conecta na janela pop-up.

## <a name="direct-methods"></a>Métodos diretos

1. Clique com o botão direito no seu dispositivo e **selecione Invocar Método Direto**. 

2. Introduza o nome do método e a carga útil na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT**  >  **Azure IoT Hub.**

## <a name="read-device-twin"></a>Ler o dispositivo gémeo

1. Clique com o botão direito no seu dispositivo e selecione **Editar O Dispositivo Twin**. 

2. Será aberto um **azure-iot-device-twin.jsno** ficheiro com o conteúdo do dispositivo twin.

## <a name="update-device-twin"></a>Atualização do dispositivo twin

1. Faça algumas edições de **tags** ou **propriedades.campo desejado.**

2. Clique à direita no **azure-iot-device-twin.jsno** ficheiro.

3. Selecione **Update Device Twin** para atualizar o dispositivo twin.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu hub IoT para o seu dispositivo, siga estes passos:
 
1. Clique com o botão direito no seu dispositivo e selecione **Enviar mensagem C2D para dispositivo**. 

2. Introduza a mensagem na caixa de entrada.

3. Os resultados serão mostrados na vista **OUTPUT**  >  **Azure IoT Hub.**

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar a extensão Azure IoT Tools para Código de Estúdio Visual com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
