---
title: Crie e gere empregos na sua aplicação Azure IoT Central  Microsoft Docs
description: Os trabalhos centrais da Azure IoT permitem capacidades de gestão de dispositivos a granel, tais como aatualização de uma propriedade do dispositivo, a definição ou a execução de um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 114946fa37ae161aeb2efd5b7cd50444c5df4c2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906705"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e faça um trabalho na sua aplicação Azure IoT Central

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos conectados em escala utilizando trabalhos. Os trabalhos permitem-lhe fazer atualizações a granel às propriedades e comandos do dispositivo. Este artigo ajuda-o a começar a usar empregos na sua própria aplicação.


## <a name="create-and-run-a-job"></a>Criar e gerir um trabalho

Esta secção mostra-lhe como criar e gerir um trabalho. Mostra como aumentar a velocidade do ventilador para várias máquinas de venda automática refrigeradas.

1. Navegue para Jobs a partir do painel de navegação.

2. Selecione **+ Novo** para criar um novo emprego.

    ![Criar novo emprego](./media/howto-run-a-job/createnewjob.png)

3. Insira um nome e descrição para identificar o trabalho que está a criar.

4. Selecione o grupo de dispositivos a que pretende que o seu trabalho se aplique. Pode ver quantos dispositivos a sua configuração de trabalho será aplicada na secção Resumo. 

5. Em seguida, escolha o tipo de trabalho a definir (propriedade ou comando). Configurar a configuração de trabalho por seleção da propriedade e definir novos valores ou escolher um comando. É possível adicionar várias propriedades de cada vez.

    ![Trabalho de configuração](./media/howto-run-a-job/configurejob.png)

6. Depois de selecionar os seus dispositivos, escolha **Correr** ou **Guardar**. O trabalho agora aparece na sua página principal do **Jobs.** Nesta perspetiva, pode ver o seu trabalho atualmente em funcionamento e a história de quaisquer trabalhos anteriormente geridos. O teu trabalho de corrida aparece sempre no topo da lista. O seu trabalho guardado pode ser aberto novamente a qualquer momento para continuar a editar ou correr.

    ![Ver trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Pode ver a história dos seus trabalhos anteriormente executados por até 30 dias.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho para ver a partir da lista. Esta visão geral contém os detalhes do trabalho, dispositivos e valores de estado do dispositivo. A partir desta visão geral, também pode selecionar **Download Job Details** para descarregar um ficheiro .csv dos seus dados de trabalho, incluindo os dispositivos e os seus valores de estado. Esta informação pode ser útil para resolução de problemas.

    ![Ver o estado do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Pare de trabalhar em funcionamento.

Para parar um trabalho de corrida, selecione-o e escolha **Parar**. As alterações ao estado do trabalho para refletir o trabalho estão paradas.

   ![Parar o emprego](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho parado

Para gerir um trabalho que está parado, selecione o trabalho parado. Escolha **correr** no painel. As alterações no estado do emprego para refletir o trabalho estão agora a funcionar novamente.

   ![Trabalho retomado](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um trabalho existente que criou, abra um trabalho que tenha sido criado e selecione **Copy**. Uma nova cópia da configuração do trabalho abre para que edite. Pode saquear ou gerir o novo emprego. 

   ![Trabalho de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Ver o estado do trabalho

Após a criação de um trabalho, a coluna **status** atualiza com a última mensagem de estado do trabalho. O quadro seguinte enumera os valores de estado possíveis:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Com Falhas               | Este trabalho falhou e não foi totalmente executado em dispositivos.  |
| Pendente              | Este trabalho ainda não começou a ser executado em dispositivos.         |
| A executar              | Este trabalho está atualmente a ser executado em dispositivos.             |
| Parada              | Este trabalho foi interrompido manualmente por um utilizador.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela que se segue enumera os valores de estado do dispositivo:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedido            | O número de dispositivos que o trabalho executou com sucesso.       |
| Com Falhas               | O número de dispositivos que o trabalho não executou.       |

### <a name="view-the-device-status"></a>Ver o estado do dispositivo

Para ver o estado do trabalho e todos os dispositivos afetados, selecione o trabalho. Para descarregar um ficheiro .csv que inclua os detalhes do trabalho, incluindo a lista de dispositivos e os seus valores de estado, selecione **Descarregamento detalhes**de trabalho . Ao lado de cada nome do dispositivo, você vê uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com Falhas               | O trabalho falhou em executar neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido eliminado, não pode selecionar o dispositivo e apresenta-se como eliminado com o ID do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar empregos na sua aplicação Azure IoT Central, eis alguns passos seguintes:

- [Utilizar conjuntos de dispositivos](howto-use-device-sets.md)
- [Gerir os seus dispositivos](howto-manage-devices.md)
- [Verifique o seu modelo de dispositivo](howto-version-device-template.md)
