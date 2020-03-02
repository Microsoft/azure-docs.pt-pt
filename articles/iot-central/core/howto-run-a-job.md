---
title: Crie e gere empregos na sua aplicação Azure IoT Central  Microsoft Docs
description: Os trabalhos centrais da Azure IoT permitem capacidades de gestão de dispositivos a granel, tais como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206829"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e faça um trabalho na sua aplicação Azure IoT Central

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos conectados em escala utilizando trabalhos. Os trabalhos permitem-lhe fazer atualizações a granel para as propriedades do dispositivo e executar comandos. Este artigo mostra-lhe como começar a usar empregos na sua própria aplicação.

## <a name="create-and-run-a-job"></a>Criar e gerir um trabalho

Esta secção mostra-lhe como criar e gerir um trabalho. Mostra como definir o limiar de luz para um grupo de dispositivos de gateway logísticos.

1. Navegue para **Jobs** a partir do painel esquerdo.

2. Selecione **+ Novo** para criar um novo emprego:

    > [!div class="mx-imgBorder"]
    > ![Criar novos](./media/howto-run-a-job/createnewjob.png) de emprego

3. Insira um nome e descrição para identificar o trabalho que está a criar.

4. Selecione o grupo de dispositivos-alvo a que pretende que o seu trabalho se aplique. Pode ver quantos dispositivos a sua configuração de trabalho se aplica na secção **Resumo.**

5. Em seguida, escolha a **Propriedade** ou **o Comando** como o tipo de trabalho para configurar. Para configurar uma configuração de trabalho de **propriedade,** selecione uma propriedade e detetete o seu novo valor. Para configurar um **Comando,** ou escolher o comando para executar. Um trabalho imobiliário pode definir várias propriedades:

    > [!div class="mx-imgBorder"]
    > ![Configurar](./media/howto-run-a-job/configurejob.png) de trabalho

6. Depois de selecionar os seus dispositivos, escolha **Correr** ou **Guardar**. O trabalho agora aparece na sua página principal do **Jobs.** Nesta página, pode ver o seu trabalho atualmente em funcionamento e o histórico de quaisquer empregos anteriormente executados ou salvos. O seu trabalho guardado pode ser aberto novamente a qualquer momento para continuar a editá-lo ou executá-lo:

    > [!div class="mx-imgBorder"]
    > ![Ver](./media/howto-run-a-job/viewjob.png) de trabalho

    > [!NOTE]
    > Pode ver 30 dias de história para os seus trabalhos anteriormente geridos.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho para ver a partir da lista. Esta visão geral contém os detalhes do trabalho, dispositivos e valores de estado do dispositivo. A partir desta visão geral, também pode selecionar **Download Job Details** para descarregar um ficheiro CSV dos seus dados de trabalho, incluindo os dispositivos e os seus valores de estado. Esta informação pode ser útil para resolução de problemas:

    > [!div class="mx-imgBorder"]
    > ![Ver o estado do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Pare de trabalhar em funcionamento.

Para parar um dos seus trabalhos de corrida, abra-o e selecione **Stop**. As alterações ao estado do trabalho para refletir o trabalho estão paradas. A secção **Resumo** mostra quais os dispositivos que completaram, falharam ou ainda estão pendentes:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho parado

Para executar um trabalho que está parado, selecione-o e, em seguida, selecione **Run**. As alterações no estado do emprego para refletir o trabalho estão agora a funcionar novamente. A secção **Resumo** continua a atualizar com os últimos progressos:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um dos seus trabalhos existentes, abra-o e selecione **Copy**. Uma cópia da configuração de trabalho abre para você editar, e **Copy** é anexado ao final do nome de trabalho. Pode salvar ou gerir o novo emprego:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Ver o estado do trabalho

Após a criação de um trabalho, a coluna **status** atualiza com a última mensagem de estado do trabalho. O quadro seguinte enumera os valores de estado possíveis:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Falhou               | Este trabalho falhou e não foi totalmente executado em dispositivos.  |
| Pendente              | Este trabalho ainda não começou a ser executado em dispositivos.         |
| A executar              | Este trabalho está atualmente a ser executado em dispositivos.             |
| Parada              | Este trabalho foi interrompido manualmente por um utilizador.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela que se segue enumera os valores de estado do dispositivo:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedido            | O número de dispositivos que o trabalho executou com sucesso.       |
| Falhou               | O número de dispositivos que o trabalho não executou.       |

### <a name="view-the-device-status"></a>Ver o estado do dispositivo

Para ver o estado do trabalho e todos os dispositivos afetados, selecione o trabalho. Para descarregar um ficheiro CSV que inclua os detalhes do trabalho, incluindo a lista de dispositivos e os seus valores de estado, selecione **Descarregue detalhes**de trabalho . Ao lado de cada nome do dispositivo, você vê uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Falhou               | O trabalho falhou em executar neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido apagado, não pode selecionar o dispositivo. Apresenta-se como eliminado com o ID do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar empregos na sua aplicação Azure IoT Central, eis alguns passos seguintes:

- [Gerir os seus dispositivos](howto-manage-devices.md)
- [Verifique o seu modelo de dispositivo](howto-version-device-template.md)
