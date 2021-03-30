---
title: Gestão de dispositivos Azure IoT c/ Visual Studio Cloud Explorer
description: Utilize o Cloud Explorer para o Visual Studio para a gestão de dispositivos Azure IoT Hub, apresentando os métodos Diretos e as opções de gestão de propriedades desejadas pela Twin.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: 6fe5a45dda6632c56b3c6714827950e25e7d26af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953189"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Use o Cloud Explorer para o Estúdio Visual para a gestão de dispositivos Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[O Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) é uma extensão útil do Visual Studio que lhe permite ver os seus recursos Azure, inspecionar as suas propriedades e realizar ações de desenvolvimento chave a partir do Estúdio Visual. Ele vem com opções de gestão que você pode usar para executar várias tarefas.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa                    |
|----------------------------|--------------------------------|
| Métodos diretos             | Faça com que um dispositivo aja como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Ler o dispositivo gémeo           | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Atualização do dispositivo twin         | Coloque um dispositivo em determinados estados, tais como a definição de um LED para verde ou a definição do intervalo de envio de telemetria para 30 minutos.         |
| Mensagens nuvem-para-dispositivo   | Envie notificações para um dispositivo. Por exemplo: "É muito provável que chova hoje. Não se esqueça de trazer um guarda-chuva.              |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte [a orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação nuvem-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos, incluindo metadados, configurações e condições. O IoT Hub persiste num dispositivo gémeo para cada dispositivo que se conecta ao mesmo. Para obter mais informações sobre os gémeos do dispositivo, consulte [Começar com os gémeos do dispositivo](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Neste artigo, aprende a usar o Cloud Explorer para Visual Studio com várias opções de gestão no seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Neste artigo, execute o Cloud Explorer para Visual Studio com várias opções de gestão.

## <a name="what-you-need"></a>O que precisa

Precisa dos seguintes pré-requisitos:

- Uma subscrição ativa do Azure.

- Um Hub Azure IoT sob a sua assinatura.

- Microsoft Visual Studio 2017 Update 9 ou mais tarde. Este artigo utiliza [o Visual Studio 2017 ou o Visual Studio 2019](https://www.visualstudio.com/vs/).

- Componente cloud Explorer do Instalador visual do Estúdio, que foi selecionado por padrão com Azure Workload.

## <a name="update-cloud-explorer-to-latest-version"></a>Atualizar o Cloud Explorer para a versão mais recente

O componente Cloud Explorer do Visual Studio Installer para o Visual Studio 2017 suporta apenas mensagens de monitorização de dispositivos para nuvem e nuvem-para-dispositivo. Para utilizar o Visual Studio 2017, descarregue e instale o mais recente [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Inscreva-se para aceder ao seu hub

1. No Estúdio Visual, selecione **View**  >  **Cloud Explorer** para abrir o Cloud Explorer.

1. Selecione o ícone de Gestão de Conta para mostrar as suas subscrições.

    ![Ícone de gestão de conta](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Se você está inscrito no Azure, as suas contas aparecem. Para entrar no Azure pela primeira vez, escolha **Adicionar uma conta**.

1. Selecione as subscrições Azure que pretende utilizar e escolha **Aplicar**.

1. Expanda a sua subscrição e, em seguida, **expanda os Hubs IoT**.  Debaixo de cada centro, podes ver os teus dispositivos para aquele centro. Clique com o botão direito de um dispositivo para aceder às opções de gestão.

    ![Opções de gestão](media/iot-hub-device-management-visual-studio/management-options-vs2019.png)

## <a name="direct-methods"></a>Métodos diretos

Para utilizar métodos diretos, faça os seguintes passos:

1. Clique com o botão direito no seu dispositivo e **selecione Invocar o Método Direto do Dispositivo**.

1. Introduza o nome do método e a carga útil no **Método Direct de Invocar** e, em seguida, selecione **OK**.

    Os resultados aparecem na **saída**.

## <a name="update-device-twin"></a>Atualização do dispositivo twin

Para editar um dispositivo gémeo, faça os seguintes passos:

1. Clique com o botão direito no seu dispositivo e selecione **Editar O Dispositivo Twin**.

   Uma **azure-iot-device-twin.jsno** ficheiro abre com o conteúdo do dispositivo twin.

1. Faça algumas edições de **tags** ou **propriedades.campos desejados** para o **azure-iot-device-twin.jsem** arquivo.

1. Prima **Ctrl+S** para atualizar o dispositivo twin.

   Os resultados aparecem na **saída**.

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

Para enviar uma mensagem do seu Hub IoT para o seu dispositivo, siga estes passos:

1. Clique com o botão direito no seu dispositivo e selecione **Enviar mensagem C2D**.

1. Introduza a mensagem no **Enviar mensagem C2D** e selecione **OK**.

   Os resultados aparecem na **saída**.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a usar o Cloud Explorer para o Visual Studio com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
