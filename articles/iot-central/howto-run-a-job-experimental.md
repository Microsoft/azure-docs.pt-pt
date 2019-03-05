---
title: Criar e executar tarefas na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Tarefas do Azure IoT Central permitem funcionalidades de gestão de dispositivos em massa, como atualizar uma propriedade do dispositivo, definição ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 8667b7747a8874451e21fbf8aea19f51b6b6252f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314233"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar uma tarefa no seu aplicativo de Central de IoT do Azure

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos ligados à escala da utilização de tarefas. A funcionalidade de tarefas permite-lhe executar atualizações em massa para propriedades do dispositivo, as definições e comandos. Este artigo descreve como começar a utilizar as tarefas em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar uma tarefa

Esta secção mostra-lhe como criar e executar uma tarefa. Cada passo passa por um exemplo que demonstra como executar uma tarefa para os dispositivos de refrigerated de venda automática de máquina que tem de ter a velocidade de ventoinha aumentada.

1. Navegue até às tarefas no painel de navegação.

1. Selecione **+ novo** para começar a criar uma nova tarefa.

    ![Criar nova tarefa](./media/howto-run-a-job-experimental/createnewjob.png)

1. Introduza um nome e descrição que o ajudam a identificar a tarefa está a criar.

1. Selecione o conjunto de dispositivo que pretende que o seu trabalho a ser aplicado. Depois de selecionar o dispositivo definida, verá no lado direito, preencha com os dispositivos dentro do conjunto de dispositivo selecionado. Se selecionar um conjunto de dispositivo quebrado, não existem dispositivos irão apresentar e verá uma mensagem que explica que o seu conjunto de dispositivos é quebrado.

1. Em seguida, selecione o tipo de tarefa que vai ser definido (uma definição, propriedade ou comando). Selecione **+** ao lado do tipo de tarefa selecionada e adicionar as suas operações pretendidas.

    ![Configurar tarefa](./media/howto-run-a-job-experimental/configurejob.png)

1. No lado direito, escolha os dispositivos que pretende executar a tarefa. Ao selecionar a caixa de verificação principal, todos os dispositivos são selecionados no conjunto de dispositivo inteiro. Ao selecionar a caixa de verificação junto ao nome, são selecionados todos os dispositivos na página atual.

1. Depois dos dispositivos pretendidos tenham sido selecionados, selecione **executar**. A tarefa aparecerão agora no principal **tarefas** página. Nesta vista, é capaz de ver o seu trabalho em execução e o histórico de qualquer anteriormente executar tarefas. Seu trabalho em execução serão sempre apresentados na parte superior da lista.

    ![Ver Tarefa](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > Poderá ver o histórico das suas tarefas de execução anterior até 30 dias.

1. Para obter uma descrição geral do seu trabalho, selecione o nome da tarefa que quer ver da sua lista. Esta descrição geral contém os detalhes da tarefa, dispositivos e os Estados do dispositivo.

    ![Ver o estado do dispositivo](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Parar uma tarefa em execução

Se desejar parar uma tarefa que está atualmente em execução, selecione o nome da tarefa em execução que pretende parar. Escolha o **parar** botão no painel. Verá que o estado da tarefa foi alterado para refletir que a tarefa foi parada.

   ![Parar tarefa](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar uma tarefa parada

Se gostaria de executar uma tarefa que está atualmente parada, selecione o nome da tarefa parada que gostaria de executar. Escolha o **executar** botão no painel. Verá que o estado da tarefa foi alterado para refletir que a tarefa está agora em execução novamente.

   ![Tarefa de retomada de forma](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>Ver o estado da tarefa

Quando uma tarefa tiver sido criada, o **estado** coluna será atualizada com a mensagem de estado mais recente da tarefa. As mensagens de estado significam o seguinte:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Esta tarefa foi executada em todos os dispositivos.              |
| Com Falhas               | Esta tarefa tem falha e não totalmente executado nos dispositivos.  |
| Pendente              | Esta tarefa ainda não tenha iniciado em execução em dispositivos.        |
| A executar              | Esta tarefa está atualmente em execução em dispositivos.             |
| Parada              | Esta tarefa foi parada manualmente por um utilizador.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos dentro da tarefa. Estes Estados do dispositivo significam o seguinte:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedido            | O número de dispositivos que a tarefa foi executada com êxito no.  |
| Com Falhas               | O número de dispositivos que a tarefa falhou a ser executadas a.      |

### <a name="view-the-device-status"></a>Ver o estado do dispositivo

Para ver o estado de cada dispositivo no trabalho, selecione o nome da tarefa. Aqui verá os detalhes do trabalho e todos os dispositivos que foram uma parte da tarefa específica. Junto a cada nome de dispositivo, verá uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | A tarefa foi executada neste dispositivo.                                     |
| Com Falhas               | A tarefa não conseguiu executar neste dispositivo. A mensagem de erro que acompanha este artigo irá mostrar mais informações.  |
| Pendente              | A tarefa ainda não executada neste dispositivo.                                  |

> [!NOTE]
> Se um dispositivo tiver sido eliminado, não será possível selecionar o dispositivo e esta será apresentada como excluído com o ID de dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu a criar tarefas na sua aplicação do Azure IoT Central, seguem-se alguns passos seguintes:

- [Utilizar conjuntos de dispositivos](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Gerir os seus dispositivos](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Versão do seu modelo de dispositivo](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
