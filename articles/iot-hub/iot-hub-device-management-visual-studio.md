---
title: Gestão de dispositivos Azure IoT c/ Visual Studio Cloud Explorer
description: Utilize o Cloud Explorer para o Estúdio Visual para a gestão de dispositivos Azure IoT Hub, com os métodos Direct e as opções de gestão de propriedades desejadas pela Twin.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73953189"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Use o Cloud Explorer para o Estúdio Visual para a gestão de dispositivos Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão útil do Estúdio Visual que lhe permite visualizar os seus recursos Azure, inspecionar as suas propriedades e realizar ações de desenvolvimento chave a partir do Visual Studio. Vem com opções de gestão que pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça um dispositivo agir como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Ler dispositivo gémeo           | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Atualizar dispositivo twin         | Coloque um dispositivo em certos estados, tais como colocar um LED a verde ou definir o intervalo de envio de telemetria para 30 minutos.         |
| Mensagens cloud-to-device   | Envie notificações para um dispositivo. Por exemplo: "É muito provável que chova hoje. Não se esqueça de trazer um guarda-chuva.              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte a orientação de [comunicação Dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos, incluindo metadados, configurações e condições. O IoT Hub persiste um dispositivo gémeo para cada dispositivo que se liga ao mesmo. Para mais informações sobre gémeos dispositivos, consulte [Começar com gémeos dispositivos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Neste artigo, aprende-se a utilizar o Cloud Explorer para o Estúdio Visual com várias opções de gestão no seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Neste artigo, execute o Cloud Explorer para o Estúdio Visual com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

Precisa dos seguintes pré-requisitos:

- Uma subscrição ativa do Azure.

- Um Hub Azure IoT sob a sua subscrição.

- Microsoft Visual Studio 2017 Update 9 ou mais tarde. Este artigo utiliza [o Visual Studio 2017 ou o Visual Studio 2019.](https://www.visualstudio.com/vs/)

- Componente do Cloud Explorer do Instalador de Estúdio Visual, que foi selecionado por padrão com a Carga de Trabalho Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Instalador de Estúdio Visual para O Estúdio Visual 2017 apenas suporta a monitorização de mensagens de dispositivos para nuvem e cloud-to-device. Para utilizar o Visual Studio 2017, descarregue e instale o mais recente [Cloud Explorer.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Inscreva-se para aceder ao seu hub

1. No Estúdio Visual, selecione **Ver** > **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone de Gestão de Conta para mostrar as suas subscrições.

    ![Ícone de Gestão de Conta](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se estiver inscrito no Azure, as suas contas aparecem. Para assinar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as subscrições Azure que pretende utilizar e escolha **Aplicar**.

1. Expanda a sua subscrição e, em seguida, expanda **os Hubs IoT**.  Debaixo de cada centro, pode ver os seus dispositivos para aquele centro. Clique num dispositivo para aceder às opções de gestão.

    ![Opções de gestão](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Métodos diretos

Para utilizar métodos diretos, faça os seguintes passos:

1. Clique no seu dispositivo e selecione **Invocar método direto**do dispositivo .

1. Introduza o nome do método e a carga útil no **Método Direct invocação**e, em seguida, selecione **OK**.

    Os resultados aparecem na **Saída**.

## <a name="update-device-twin"></a>Atualizar dispositivo twin

Para editar um dispositivo gémeo, faça os seguintes passos:

1. Clique no seu dispositivo e selecione **Editar Dispositivo Twin**.

   Um ficheiro **azure-iot-device-twin.json** abre com o conteúdo do dispositivo twin.

1. Faça **algumas** edificações de tags ou **propriedades.campos desejados** para o ficheiro **azure-iot-device-twin.json.**

1. Prima **Ctrl+S** para atualizar o dispositivo twin.

   Os resultados aparecem na **Saída**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu Hub IoT para o seu dispositivo, siga estes passos:

1. Clique no seu dispositivo e selecione **Enviar mensagem C2D**.

1. Introduza a mensagem no **Enviar mensagem C2D** e selecione **OK**.

   Os resultados aparecem na **Saída**.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar o Cloud Explorer para o Visual Studio com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
