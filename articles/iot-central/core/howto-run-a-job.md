---
title: Crie e gere empregos na sua aplicação Azure IoT Central / Microsoft Docs
description: Os trabalhos Azure IoT Central permitem capacidades de gestão de dispositivos a granel, tais como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: philmea
ms.author: philmea
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: b8106c154a91d1e823a124a90f7571b7f52ae8cb
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682154"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e executar um emprego na sua aplicação Azure IoT Central

Pode utilizar a Azure IoT Central para gerir os seus dispositivos conectados em escala através de empregos. Os trabalhos permitem-lhe fazer atualizações a granel para propriedades de dispositivos e nuvem e executar comandos. Este artigo mostra-lhe como começar a usar empregos na sua própria aplicação.

## <a name="create-and-run-a-job"></a>Criar e gerir um emprego

O exemplo que se segue mostra como criar e executar um trabalho para definir o limiar de luz para um grupo de dispositivos de gateway logístico. Usas o assistente de trabalho para criar e gerir empregos. Podes poupar um emprego para correr mais tarde.

1. No painel esquerdo, selecione **Jobs**.

1. Selecione **+ Novo emprego**.

1. Na página de configurar o **seu trabalho,** insira um nome e descrição para identificar o trabalho que está a criar.

1. Selecione o grupo de dispositivos-alvo a que deseja que o seu trabalho se aplique. Pode ver quantos dispositivos a sua configuração de trabalho se aplica abaixo da seleção do **grupo Dispositivo.**

1. Escolha **propriedade Cloud,** **Propriedade,** ou **Comando** como o tipo **de Trabalho**:

    Para configurar um trabalho **de propriedade,** selecione um imóvel e desafie o seu novo valor. Para configurar um trabalho de **Comando,** escolha o comando para executar. Um trabalho de propriedade pode definir várias propriedades.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Screenshot que mostra seleções para criar um trabalho de propriedade chamado set Light Threshold":::

    **Selecione Save and exit** para adicionar o trabalho à lista de empregos guardados na página **Jobs.** Mais tarde pode voltar a trabalhar na lista de empregos salvos.

1. Selecione **Seguinte** para mover para a página **Opções de Entrega.** A página **Opções de Entrega** permite definir as opções de entrega para este trabalho: **Loteamento** e **limiar de cancelamento.**

    Os lotes permitem-lhe trabalhar escalonando para um grande número de dispositivos. O trabalho é dividido em vários lotes e cada lote contém um subconjunto dos dispositivos. Os lotes são em fila e executados em sequência.

    O limiar de cancelamento permite-lhe cancelar automaticamente um trabalho se o número de erros exceder o limite estabelecido. O limiar pode aplicar-se a todos os dispositivos do trabalho ou a lotes individuais.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Screenshot da página de opções de entrega de assistente de trabalho":::

1. Selecione **Seguinte** para mover para a página **Agendar.** A página **Agenda** permite-lhe ativar um horário para executar o trabalho no futuro:

    Escolha uma opção de recorrência para o horário. Pode preparar um trabalho para gerir:

    * Único
    * Diárias
    * Semanais

    Marque uma data de início e hora para um trabalho agendado. A data e a hora são específicas do seu fuso horário e não da hora local do dispositivo.

    Para terminar um horário recorrente, escolha:

    * **Neste dia** para definir uma data de fim para o horário.
    * **Depois de** definir o número de vezes para executar o trabalho.

    Os trabalhos programados funcionam sempre nos dispositivos de um grupo de dispositivos, mesmo que a adesão ao grupo de dispositivos mude ao longo do tempo.

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule.png" alt-text="Screenshot da página de opções de agenda de assistente de trabalho":::

1. Selecione **Seguinte** para mover para a página **'Revisão'.** A página **'Avaliação'** mostra os detalhes da configuração do trabalho. Selecione **Agenda** para agendar o trabalho:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-review.png" alt-text="Screenshot da página de revisão do assistente de trabalho programado":::

1. A página de detalhes do trabalho mostra informações sobre trabalhos programados. Quando o trabalho programado executa, vê-se uma lista das situações de trabalho. A execução programada também faz parte da lista de trabalho dos **últimos 30** dias.

    Nesta página, pode **cancelar** o trabalho ou **editar** o trabalho agendado. Pode voltar a um emprego programado da lista de trabalhos programados.

    :::image type="content" source="media/howto-run-a-job/job-schedule-details.png" alt-text="Screenshot da página de detalhes de trabalho programado":::

1. No assistente de trabalho, pode optar por não agendar um emprego e executá-lo imediatamente. A imagem que se segue mostra um trabalho sem um horário que está pronto para ser executado imediatamente. Selecione **Executar** para executar o trabalho:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-immediate.png" alt-text="Screenshot da página de revisão do assistente de trabalho":::

1. Um trabalho passa *por fases pendentes,* *em execução* e *concluídas.* Os detalhes da execução do trabalho contêm métricas de resultados, detalhes de duração e uma grelha de lista de dispositivos.

    Quando o trabalho estiver concluído, pode selecionar **o registo de Resultados** para descarregar um ficheiro CSV dos seus dados de trabalho, incluindo os dispositivos e os seus valores de estado. Esta informação pode ser útil para a resolução de problemas.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Screenshot que mostra o estado do dispositivo":::

1. O trabalho aparece agora na lista **dos últimos 30 dias** na página **Jobs.** Esta página mostra atualmente a executar empregos e o histórico de quaisquer trabalhos anteriormente executados ou salvos.

    > [!NOTE]
    > Pode ver 30 dias de história para os seus trabalhos anteriormente geridos.

## <a name="manage-jobs"></a>Gerir tarefas

Para parar um trabalho de funcionamento, abra-o e **selecione Stop**. O estatuto de trabalho muda para refletir que o trabalho está parado. A secção **Resumo** mostra quais os dispositivos que completaram, falharam ou ainda estão pendentes.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Screenshot que mostra um trabalho em execução e o botão para parar um trabalho":::

Quando um trabalho está num estado parado, pode selecionar **Continue** a executar o trabalho. O estatuto de trabalho muda para refletir que o trabalho está agora a funcionar novamente. A secção **Resumo** continua a atualizar-se com os últimos progressos.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Screenshot que mostra um trabalho parado e o botão para continuar um trabalho":::

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um trabalho existente, selecione um trabalho executado. Selecione **Copiar** na página de resultados do trabalho ou na página de detalhes do trabalho:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Screenshot que mostra o botão de cópia":::

Uma cópia da configuração de trabalho abre para que você edite, e **Copy** é anexado ao nome do trabalho.

## <a name="view-job-status"></a>Ver o estado de tarefa

Após a criação de um trabalho, a coluna **Status** atualiza-se com a última mensagem de estado de trabalho. A tabela a seguir enumera os valores possíveis *do estado do trabalho:*

| Mensagem de estado       | Significado de estado                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho funcionou em todos os dispositivos.              |
| Com falhas               | Este trabalho falhou e não funcionou completamente em dispositivos.  |
| Pendente              | Este trabalho ainda não começou a funcionar em dispositivos.         |
| Em Execução              | Este trabalho está atualmente a funcionar em dispositivos.             |
| Parada              | Um utilizador parou manualmente este trabalho.           |
| Cancelado             | Este trabalho foi cancelado porque o limiar estabelecido na página **de opções de entrega** foi ultrapassado. |

A mensagem de estado é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir enumera os valores de estado possíveis do *dispositivo:*

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

Para descarregar um ficheiro CSV que inclua os dados do trabalho e a lista de dispositivos e os seus valores de estado, selecione **Registo de Resultados**.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Pode filtrar a lista de **dispositivos** na página de detalhes do Trabalho selecionando o ícone do filtro. Pode filtrar no **campo de ID** ou Status **do** dispositivo:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Screenshot que mostra seleções para filtrar uma lista de dispositivos.":::

## <a name="customize-columns-in-the-device-list"></a>Personalize as colunas na lista de dispositivos

Pode adicionar colunas à lista de dispositivos selecionando o ícone das opções de coluna:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Screenshot que mostra o ícone para opções de coluna.":::

Utilize a caixa de diálogo **de opções colunas** para escolher as colunas da lista de dispositivos. Selecione as colunas que pretende visualizar, selecione a seta direita e, em seguida, selecione **OK**. Para selecionar todas as colunas disponíveis, escolha **Selecione todas.** As colunas selecionadas aparecem na lista de dispositivos.

As colunas selecionadas persistem numa sessão de utilizador ou em sessões de utilizador que tenham acesso à aplicação.

## <a name="rerun-jobs"></a>Rerun empregos

Pode repetir um trabalho que falhou nos dispositivos. Selecione **Rerun em falha:**

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Screenshot que mostra o botão para reexame de um trabalho em dispositivos falhados.":::

Introduza um nome de trabalho e descrição e, em seguida, selecione **Rerun job**. Um novo trabalho é submetido para tentar novamente a ação em dispositivos falhados.

> [!NOTE]
> Não podes gerir mais de cinco empregos ao mesmo tempo a partir de uma aplicação da Azure IoT Central.
>
> Quando um trabalho está concluído e apaga-se um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo aparece como eliminada no nome do dispositivo. O link de detalhes não está disponível para o dispositivo eliminado.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a criar empregos na sua aplicação Azure IoT Central, eis alguns próximos passos:

- [Faça a gestão dos seus dispositivos](howto-manage-devices.md)
- [Versão o seu modelo de dispositivo](howto-version-device-template.md)
