---
title: Criar e executar tarefas na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Tarefas do Azure IoT Central permitem funcionalidades de gestão de dispositivos em massa, como atualizar uma propriedade do dispositivo, definição ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199856"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar uma tarefa no seu aplicativo de Central de IoT do Azure

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos ligados à escala da utilização de tarefas. As tarefas permitem em massa a atualizações para as propriedades dos dispositivos, definições e comandos. Este artigo explica como começar a utilizar as tarefas em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar uma tarefa

Esta secção mostra-lhe como criar e executar uma tarefa. Ele mostra como aumentar a velocidade de ventoinha para várias máquinas de venda automática refrigerated.

1. Navegue até às tarefas no painel de navegação.

1. Selecione **+ novo** para criar uma nova tarefa.

    ![Criar nova tarefa](./media/howto-run-a-job/createnewjob.png)

1. Introduza um nome e descrição para identificar a tarefa que está a criar.

1. Selecione o conjunto de dispositivo que pretende aplicar ao seu trabalho. Depois de definir a selecionar o dispositivo, verá no lado direito, preencha com os dispositivos no conjunto de dispositivos. Se selecionar um conjunto de dispositivo quebrada, sem dispositivos apresentam e verá uma mensagem que o seu conjunto de dispositivo foi interrompido.

1. Em seguida, selecione o tipo de tarefa para definir (uma definição, propriedade ou comando). Selecione **+** ao lado do tipo de tarefa selecionada e adicionar as suas operações.

    ![Configurar tarefa](./media/howto-run-a-job/configurejob.png)

1. No lado direito, selecione os dispositivos que pretende executar a tarefa. Ao selecionar a caixa de verificação principal, todos os dispositivos são selecionados no conjunto de dispositivo inteiro. Ao selecionar a caixa de verificação junto **nome**, são selecionados todos os dispositivos na página atual.

1. Depois de selecionar os seus dispositivos, escolha **execute** ou **guardar**. A tarefa é agora apresentado no principal **tarefas** página. Nesta vista, pode ver a tarefa em execução e o histórico de qualquer anteriormente executar tarefas. Seu trabalho em execução sempre é exibido na parte superior da lista. Seu trabalho guardado pode ser aberto novamente em qualquer altura para continuar a editar ou para executar.

    ![Ver Tarefa](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Pode ver o histórico das suas tarefas de execução anterior até 30 dias.

1. Para obter uma descrição geral do seu trabalho, selecione a tarefa para ver a partir da lista. Esta descrição geral contém os detalhes da tarefa, dispositivos e valores de estado do dispositivo. Esta descrição geral, pode também selecionar **detalhes da tarefa de transferência** para transferir um ficheiro. csv de detalhes do seu trabalho, incluindo os dispositivos e os respetivos valores de estado. Estas informações podem ser úteis para resolução de problemas.

    ![Ver o estado do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Parar uma tarefa em execução

Para parar uma tarefa em execução, selecione-o e escolha **parar** no painel. As alterações de estado de tarefa para refletir que a tarefa está parada.

   ![Parar tarefa](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar uma tarefa parada

Para executar uma tarefa que está atualmente parada, selecione a tarefa de paragem. Escolher **executar** no painel. As alterações de estado de tarefa para refletir a tarefa está agora em execução novamente.

   ![Tarefa de retomada de forma](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Uma tarefa de cópia

Para copiar uma tarefa existente que criou, selecione-a partir da página de tarefas principal e selecione **cópia**. É aberta uma nova cópia da configuração da tarefa para editar. Pode guardar ou executar a tarefa de novo. Se foram efetuadas quaisquer alterações ao seu conjunto de dispositivo selecionado, eles são refletidos nesta tarefa copiado para editar.

   ![Tarefa de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Ver o estado da tarefa

Depois de uma tarefa é criada, o **estado** atualizações de coluna com a mensagem de estado mais recente da tarefa. A tabela seguinte lista os valores de estado possíveis:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Esta tarefa foi executada em todos os dispositivos.              |
| Com Falhas               | Esta tarefa tem falha e não totalmente executado nos dispositivos.  |
| Pendente              | Esta tarefa ainda não começou a executar em dispositivos.         |
| A executar              | Esta tarefa está atualmente em execução em dispositivos.             |
| Parada              | Esta tarefa foi parada manualmente por um utilizador.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela seguinte lista os valores de estado do dispositivo possíveis:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedido            | O número de dispositivos que o trabalho executado com êxito no.       |
| Com Falhas               | O número de dispositivos que não conseguiu executar a tarefa.       |

### <a name="view-the-device-status"></a>Ver o estado do dispositivo

Para ver o estado do trabalho e todos os dispositivos afetados, selecione a tarefa. Para transferir um ficheiro. csv que inclui os detalhes da tarefa, incluindo a lista de dispositivos e os respetivos valores de estado, selecione **transferir os detalhes da tarefa**. Junto a cada nome de dispositivo, verá uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | A tarefa foi executada neste dispositivo.                                     |
| Com Falhas               | A tarefa não conseguiu executar neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | A tarefa ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido eliminado, não é possível selecionar o dispositivo e apresenta como excluído com o ID de dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que já aprendeu a criar tarefas na sua aplicação do Azure IoT Central, seguem-se alguns passos seguintes:

- [Utilizar conjuntos de dispositivos](howto-use-device-sets.md)
- [Gerir os seus dispositivos](howto-manage-devices.md)
- [Versão do seu modelo de dispositivo](howto-version-devicetemplate.md)
