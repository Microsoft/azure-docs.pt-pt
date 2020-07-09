---
title: Crie e gere empregos na sua aplicação Azure IoT Central / Microsoft Docs
description: Os trabalhos Azure IoT Central permitem capacidades de gestão de dispositivos a granel, tais como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609747"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e executar um emprego na sua aplicação Azure IoT Central

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos conectados em escala utilizando trabalhos. Os trabalhos permitem-lhe fazer atualizações a granel para as propriedades do dispositivo e executar comandos. Este artigo mostra-lhe como começar a usar empregos na sua própria aplicação.

## <a name="create-and-run-a-job"></a>Criar e gerir um emprego

Esta secção mostra-lhe como criar e gerir um emprego. Mostra como definir o limiar de luz para um grupo de dispositivos de gateway logístico.

1. Navegue para **Jobs** a partir do painel esquerdo.

2. Selecione **+ Novo** para criar um novo emprego:

    ![Criar novo emprego](./media/howto-run-a-job/create-new-job.png)

3. Insira um nome e descrição para identificar o trabalho que está a criar.

4. Selecione o grupo de dispositivos alvo a que deseja que o seu trabalho se aplique. Pode ver quantos dispositivos a sua configuração de trabalho se aplica na secção **Resumo.**

5. Em seguida, escolha **propriedade Cloud,** **Propriedade**ou **Comando** como o tipo de trabalho para configurar. Para configurar uma configuração de trabalho **de propriedade,** selecione um imóvel e desa cise o seu novo valor. Para configurar um **Comando,** escolha o comando para executar. Um trabalho de propriedade pode definir várias propriedades:

    ![Configure trabalho](./media/howto-run-a-job/configure-job.png)

6. Depois de criar o seu trabalho, escolha **Executar** ou **Guardar**. O trabalho aparece agora na sua página principal **do Jobs.** Nesta página, você pode ver o seu trabalho atualmente em execução e o histórico de quaisquer trabalhos anteriormente executados ou salvos. O seu trabalho guardado pode ser aberto novamente a qualquer momento para continuar a editá-lo ou executá-lo:

    ![Ver trabalho](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Pode ver 30 dias de história para os seus trabalhos anteriormente geridos.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho para ver na lista. Esta visão geral contém os dados do trabalho, dispositivos e valores de estado do dispositivo. A partir desta visão geral, também pode selecionar **Download Job Details** para descarregar um ficheiro CSV dos seus dados de trabalho, incluindo os dispositivos e os seus valores de estado. Estas informações podem ser úteis para a resolução de problemas:

    ![Ver o estado do dispositivo](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gerir tarefas

Para parar um dos seus trabalhos de funcionamento, abra-o e **selecione Stop**. As alterações ao estatuto de trabalho para refletir o trabalho estão paradas. A secção **Resumo** mostra quais os dispositivos que completaram, falharam ou ainda estão pendentes.

Para executar um trabalho que está parado, selecione-o e, em seguida, selecione **Run**. As alterações ao estatuto de trabalho para refletir o trabalho estão agora a funcionar novamente. A secção **Resumo** continua a atualizar-se com os últimos progressos.

![Gerir o trabalho](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um dos seus trabalhos existentes, selecione-o na página **Jobs** e selecione **Copy**. Uma cópia da configuração de trabalho abre para que você edite, e **Copy** é anexado ao nome do trabalho. Pode salvar ou gerir o novo emprego:

![Trabalho de cópia](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Após a criação de um trabalho, a coluna **Status** atualiza-se com a última mensagem de estado do trabalho. A tabela a seguir enumera os valores de estado possíveis:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Falhou               | Este trabalho falhou e não foi executado totalmente em dispositivos.  |
| Pendente              | Este trabalho ainda não começou a ser executado em dispositivos.         |
| A executar              | Este trabalho está atualmente a ser executado em dispositivos.             |
| Parada              | Este trabalho foi interrompido manualmente por um utilizador.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir enumera os valores de estado possíveis do dispositivo:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Bem-sucedido            | O número de dispositivos em que o trabalho foi executado com sucesso.       |
| Falhou               | O número de dispositivos em que o trabalho falhou em executar.       |

### <a name="view-the-device-status-values"></a>Ver os valores de estado do dispositivo

Para ver o estado do trabalho e todos os dispositivos afetados, abra o trabalho. Ao lado de cada nome do dispositivo, vê uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho executado neste dispositivo.                                     |
| Falhou               | O trabalho falhou em executar neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

Para descarregar um ficheiro CSV que inclua os detalhes do trabalho e a lista de dispositivos e seus valores de estado, selecione **Download**.

### <a name="filter-the-list-of-devices"></a>Filtrar a lista de dispositivos

Pode filtrar a lista de dispositivos na página de detalhes do trabalho selecionando o ícone do filtro. Pode filtrar nos campos **de ID** ou **Status** do dispositivo:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrar a lista de dispositivos":::

### <a name="customize-columns-in-the-device-list"></a>Personalize as colunas na lista de dispositivos

Pode escolher colunas adicionais para visualizar na lista de dispositivos selecionando o ícone de opções de colunas:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Opções de coluna":::

Vê um diálogo que lhe permite escolher as colunas a visualizar na lista de dispositivos. Selecione as colunas que pretende visualizar, selecione o ícone da seta direita e o selecionado **OK**. Para selecionar todas as colunas disponíveis, consulte **selecione todas as colunas:**

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Diálogo do selecionador de colunas":::

As colunas selecionadas apresentam-se na lista do dispositivo:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Selecione colunas":::

As colunas selecionadas são persistiu numa sessão de utilizador ou em sessões de utilizador que tenham acesso à aplicação.

## <a name="rerun-jobs"></a>Rerun empregos

Pode repetir um trabalho que falhou nos dispositivos. Selecione **Rerun:**

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Recandidatura a um emprego":::

Introduza um nome de trabalho e descrição e, em seguida, selecione **Rerun job**. É submetido um novo trabalho para voltar a tentar a ação em dispositivos falhados:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Rerun dispositivos falhados":::

> [!NOTE]
> Não se pode executar mais de cinco empregos ao mesmo tempo a partir de uma aplicação da IoT Central.

> [!NOTE]
> Quando um trabalho está concluído e elimina um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo mostra como eliminado no nome do dispositivo e a ligação de detalhes do dispositivo não está disponível para o dispositivo eliminado.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a criar empregos na sua aplicação Azure IoT Central, eis alguns próximos passos:

- [Faça a gestão dos seus dispositivos](howto-manage-devices.md)
- [Versão o seu modelo de dispositivo](howto-version-device-template.md)
