---
title: Crie e gere empregos na sua aplicação Azure IoT Central / Microsoft Docs
description: Os trabalhos Azure IoT Central permitem capacidades de gestão de dispositivos a granel, tais como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797841"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e executar um emprego na sua aplicação Azure IoT Central

Pode utilizar o Microsoft Azure IoT Central para gerir os seus dispositivos conectados em escala através de trabalhos. Os trabalhos permitem-lhe fazer atualizações a granel para as propriedades do dispositivo e executar comandos. Este artigo mostra-lhe como começar a usar empregos na sua própria aplicação.

## <a name="create-and-run-a-job"></a>Criar e gerir um emprego

Esta secção mostra-lhe como criar e executar um trabalho sob a forma de definir o limiar de luz para um grupo de dispositivos de gateway logístico.

1. No painel esquerdo, selecione **Jobs**.

2. Selecione **+ Novo**.

   ![Screenshot que mostra seleções para criar um emprego.](./media/howto-run-a-job/create-new-job.png)

3. Insira um nome e descrição para identificar o trabalho que está a criar.

4. Selecione o grupo de dispositivos-alvo a que deseja que o seu trabalho se aplique. Pode ver quantos dispositivos a sua configuração de trabalho se aplica na secção **Resumo.**

5. Escolha **propriedade Cloud,** **Propriedade,** ou **Comando** como o tipo de trabalho para configurar. 

   Para configurar uma configuração de trabalho **de propriedade,** selecione um imóvel e desa cise o seu novo valor. Para configurar uma configuração de trabalho **de comando,** escolha o comando a executar. Um trabalho de propriedade pode definir várias propriedades.

   ![Screenshot que mostra seleções para criar um trabalho de propriedade chamado set Light Threshold.](./media/howto-run-a-job/configure-job.png)

6. Selecione **Executar** ou **Guardar**. O trabalho aparece agora na sua página principal **do Jobs.** Nesta página, você pode ver o seu trabalho atualmente em execução e o histórico de quaisquer trabalhos anteriormente executados ou salvos. Pode reabrir o seu trabalho guardado a qualquer momento para continuar a editá-lo ou executá-lo.

   ![Screenshot que mostra o nome, estado e descrição de um trabalho criado.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Pode ver 30 dias de história para os seus trabalhos anteriormente geridos.

7. Selecione o trabalho guardado e execute-o selecionando o botão **Executar.** 

   A caixa de diálogo **run your job?** Confirme selecionando o botão **de trabalho Executar.** 

   ![Screenshot da caixa de diálogo que confirma que deseja executar um trabalho.](./media/howto-run-a-job/run-job.png)

8. Um trabalho passa pelas fases de pendente, corrida e conclusão. Os detalhes da execução do trabalho contêm métricas de resultados, detalhes de duração e uma grelha de lista de dispositivos. 

   A partir desta visão geral, também pode selecionar **o registo de Resultados** para descarregar um ficheiro CSV dos seus dados de trabalho, incluindo os dispositivos e os seus valores de estado. Esta informação pode ser útil para a resolução de problemas.

   ![Screenshot que mostra o estado do dispositivo.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gerir tarefas

Para parar um trabalho de funcionamento, abra-o e **selecione Stop**. O estatuto de trabalho muda para refletir que o trabalho está parado. A secção **Resumo** mostra quais os dispositivos que completaram, falharam ou ainda estão pendentes.

![Screenshot que mostra um trabalho em execução e o botão para parar um trabalho.](./media/howto-run-a-job/manage-job.png)

Depois de um trabalho estar parado, pode selecionar **Continue** a executar o trabalho. O estatuto de trabalho muda para refletir que o trabalho está agora a funcionar novamente. A secção **Resumo** continua a ser atualizada com os últimos progressos.

![Screenshot que mostra um trabalho parado e o botão para continuar um trabalho.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um dos seus trabalhos existentes, selecione-o na página **Jobs** e selecione **detalhes de Trabalho**. Aparece a página **de detalhes do Trabalho.** 

![Screenshot que mostra a página para detalhes do trabalho.](./media/howto-run-a-job/job-details.png)

Selecione **Copiar**.

![Screenshot que mostra o botão Copiar.](./media/howto-run-a-job/job-details-copy.png)

Uma cópia da configuração de trabalho abre para que você edite, e **Copy** é anexado ao nome do trabalho. Podes salvar ou gerir o novo emprego.

![Screenshot que mostra uma cópia da configuração do trabalho.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Após a criação de um trabalho, a coluna **Status** é atualizada com a última mensagem de estado para o trabalho. A tabela a seguir enumera os valores possíveis do estado do trabalho:

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho funcionou em todos os dispositivos.              |
| Com falhas               | Este trabalho falhou e não correu totalmente em dispositivos.  |
| Pendente              | Este trabalho ainda não começou a funcionar em dispositivos.         |
| Em Execução              | Este trabalho está atualmente a funcionar em dispositivos.             |
| Parada              | Um utilizador parou manualmente este trabalho.           |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir enumera os valores de estado possíveis do dispositivo:

| Mensagem de estado       | Significado de estado                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Com êxito            | O número de dispositivos em que o trabalho funcionou com sucesso.       |
| Com falhas               | O número de dispositivos que o trabalho falhou.       |

Para ver o estado do trabalho e todos os dispositivos afetados, abra o trabalho. Ao lado de cada nome do dispositivo, vê uma das seguintes mensagens de estado:

| Mensagem de estado       | Significado de estado                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho correu neste dispositivo.                                     |
| Com falhas               | O trabalho falhou neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não está a funcionar neste dispositivo.                                   |

Para descarregar um ficheiro CSV que inclua os detalhes do trabalho e a lista de dispositivos e seus valores de estado, selecione **Download**.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Pode filtrar a lista de **dispositivos** na página de detalhes do Trabalho selecionando o ícone do filtro. Pode filtrar no **campo de ID** ou Status **do** dispositivo.

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Screenshot que mostra seleções para filtrar uma lista de dispositivos.":::

## <a name="customize-columns-in-the-device-list"></a>Personalize as colunas na lista de dispositivos

Pode escolher colunas adicionais para visualizar na lista de dispositivos selecionando o ícone de opções de coluna.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Screenshot que mostra o ícone para opções de coluna.":::

Uma caixa de diálogo permite-lhe escolher as colunas a visualizar na lista de dispositivos. Selecione as colunas que pretende visualizar, selecione a seta direita e, em seguida, selecione **OK**. Para selecionar todas as colunas disponíveis, consulte **Selecione todas.**

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Screenshot que mostra a caixa de diálogo para escolher colunas para exibir.":::

As colunas selecionadas aparecem na lista de dispositivos.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Screenshot que mostra colunas selecionadas na lista de dispositivos.":::

As colunas selecionadas são persistiu numa sessão de utilizador ou em sessões de utilizador que tenham acesso à aplicação.

## <a name="rerun-jobs"></a>Rerun empregos

Pode repetir um trabalho que falhou nos dispositivos. Selecione **Rerun em falha**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Screenshot que mostra o botão para reexame de um trabalho em dispositivos falhados.":::

Introduza um nome de trabalho e descrição e, em seguida, selecione **Rerun job**. Um novo trabalho é submetido para tentar novamente a ação em dispositivos falhados.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Screenshot que mostra a caixa de diálogo para re-runque dispositivos falhados.":::

> [!NOTE]
> Não podes gerir mais de cinco empregos ao mesmo tempo a partir de uma aplicação da Azure IoT Central.
>
> Quando um trabalho está concluído e apaga-se um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo aparece como eliminada no nome do dispositivo. O link de detalhes não está disponível para o dispositivo eliminado.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar empregos na sua aplicação Azure IoT Central, eis alguns próximos passos:

- [Faça a gestão dos seus dispositivos](howto-manage-devices.md)
- [Versão o seu modelo de dispositivo](howto-version-device-template.md)
