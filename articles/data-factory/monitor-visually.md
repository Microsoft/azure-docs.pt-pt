---
title: Monitorizar visualmente o Azure Data Factory
description: Saiba como monitorizar visualmente as fábricas de dados do Azure
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d177513af9f0ee4fcadb1ea316edf1ad8cb89e5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783664"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorizar visualmente o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uma vez criado e publicado um oleoduto na Azure Data Factory, pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Pode monitorizar todo o seu oleoduto funciona de forma nativa na experiência do utilizador da Azure Data Factory. Para abrir a experiência de monitorização, selecione o **telha Monitor & Gerir** na lâmina de fábrica de dados do [portal Azure](https://portal.azure.com/). Se já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda.

Por padrão, todas as operações da fábrica de dados são exibidas no fuso horário local do navegador. Se alterar o fuso horário, todos os campos de data/hora encaixam-se no que selecionou.

## <a name="monitor-pipeline-runs"></a>Monitorizar execuções de pipeline

A visão de monitorização predefinida é a lista de gasodutos desencadeados no período de tempo selecionado. Pode alterar o intervalo de tempo e filtrar por estado, nome do gasoduto ou anotação. Hover sobre o gasoduto específico executado para obter ações específicas, tais como a repetição e o relatório de consumo.

![Vista da lista para monitorização de gasodutos](media/monitor-visually/pipeline-runs.png)

A grelha de funcionação do gasoduto contém as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do pipeline | Nome do oleoduto |
| Início de execução | Data e hora de início para a execução do gasoduto (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Fim de execução | Data e hora de fim do gasoduto (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Desencadeado por | O nome do gatilho que iniciou o oleoduto |
| Estado | **Falhado,** **Bem sucedido,** **Em Curso,** **Cancelado,** ou **Em Fila** |
| Anotações | Etiquetas filtradas associadas a um gasoduto  |
| Parâmetros | Parâmetros para a execução do gasoduto (pares de nome/valor) |
| Erro | Se o gasoduto falhou, o erro de execução |
| o ID da Execução | ID da corrida do gasoduto |

É necessário selecionar manualmente o botão **Refresh** para atualizar a lista de gasodutos e de execuções de atividade. A autorefresh não está atualmente apoiada.

![Botão Atualizar](media/monitor-visually/refresh.png)

Para ver os resultados de uma execução de depurar, selecione o **separador Debug.**

![Selecione o ícone de depurar ativo ver corre](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Monitorização de execuções de atividade

Para obter uma visão detalhada das operações individuais de uma corrida específica do gasoduto, clique no nome do gasoduto.

![Ver execuções de atividades](media/monitor-visually/view-activity-runs.png)

A visualização da lista mostra que as operações correspondem a cada corrida de gasodutos. Hover sobre a atividade específica executada para obter informações específicas de execução, tais como a entrada JSON, saída JSON, e experiências de monitorização detalhadas específicas da atividade.

![Existe informação sobre salesAnalyticsMLPipeline, seguida de uma lista de atividades.](media/monitor-visually/activity-runs.png)

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da Atividade | Nome da atividade dentro do oleoduto |
| Tipo de Atividade | Tipo de atividade, como **Copy,** **ExecuteDataFlow,** ou **AzureMLExecutePipeline** |
| Ações | Ícones que lhe permitem ver informações de entrada JSON, informações de saída JSON ou experiências de monitorização detalhadas específicas da atividade | 
| Início de execução | Data e hora de início da atividade executada (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Estado | **Falhado,** **bem sucedido,** **em curso,** ou **cancelado** |
| Runtime de Integração | Qual integração Runtime a atividade foi executado em |
| Propriedades do Utilizador | Propriedades definidas pelo utilizador da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| o ID da Execução | ID da atividade executado |

Se uma atividade falhar, pode ver a mensagem de erro detalhada clicando no ícone na coluna de erro. 

![Uma notificação aparece com detalhes de erro, incluindo código de erro, tipo de falha e detalhes de erro.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Promover propriedades de utilizadores para monitorizar

Promover qualquer propriedade de atividade de pipeline como propriedade do utilizador para que se torne uma entidade que você monitorize. Por exemplo, pode promover as propriedades **de Origem** e **Destino** da atividade de cópia no seu pipeline como propriedades do utilizador.

> [!NOTE]
> Só é possível promover até cinco propriedades de atividade de gasoduto como propriedades do utilizador.

![Criar propriedades de utilizador](media/monitor-visually/promote-user-properties.png)

Depois de criar as propriedades do utilizador, pode monitorá-las nas vistas da lista de monitorização.

![Adicione colunas para propriedades de utilizador à lista de execuções de atividades](media/monitor-visually/choose-user-properties.png)

 Se a fonte para a atividade da cópia for um nome de mesa, pode monitorizar o nome da tabela de origem como uma coluna na vista da lista para a atividade.

![A atividade executa a lista com colunas para propriedades do utilizador](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Re-run de gasodutos e atividades

Para refazer um gasoduto que já tenha corrido desde o início, paire sobre a corrida específica do gasoduto e selecione **Rerun**. Se selecionar vários oleodutos, pode utilizar o botão **Rerun** para executá-los todos.

![Reencaúndio de um oleoduto](media/monitor-visually/rerun-pipeline.png)

Se desejar refazer a partir de um ponto específico, pode fazê-lo a partir da vista da atividade. Selecione a atividade a partir daí iniciada e selecione **Rerun da atividade**. 

![Repercuta uma atividade](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Repetição da atividade falhada

Se uma atividade falhar, o tempo fora ou for cancelada, pode voltar a fazer o pipeline a partir dessa atividade falhada selecionando **a Repetição da atividade falhada**.

![Rerun atividade falhada](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Ver refazer a história

Pode ver o histórico de repetição de todas as correções de pipeline na vista da lista.

![Ver histórico](media/monitor-visually/rerun-history-1.png)

Também pode ver a história da repetição para uma determinada corrida de gasodutos.

![Ver história para uma corrida de gasodutos](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Monitorizar o consumo

Pode ver os recursos consumidos por um oleoduto executado clicando no ícone de consumo ao lado da corrida. 

![Screenshot que mostra onde você pode ver os recursos consumidos por um oleoduto.](media/monitor-visually/monitor-consumption-1.png)

Clicar no ícone abre um relatório de consumo dos recursos utilizados por esse gasoduto. 

![Monitorizar o consumo](media/monitor-visually/monitor-consumption-2.png)

Pode ligar estes valores à [calculadora de preços Azure](https://azure.microsoft.com/pricing/details/data-factory/) para estimar o custo do gasoduto. Para obter mais informações sobre os preços da Azure Data Factory, consulte [os preços de Compreensão](pricing-concepts.md).

> [!NOTE]
> Estes valores devolvidos pela calculadora de preços são uma estimativa. Não reflete a quantia exata que será faturada pela Azure Data Factory 

## <a name="gantt-views"></a>Vistas de Gantt

Um gráfico gantt é uma vista que permite ver a história da corrida ao longo de um intervalo de tempo. Ao mudar para uma vista de Gantt, você verá todas as corridas de gasodutos agrupadas pelo nome apresentado como barras em relação ao tempo que o percurso demorou. Também pode agrupar-se por anotações/tags que criou no seu oleoduto. A vista Gantt também está disponível ao nível da atividade.

![Exemplo de um gráfico de Gantt](media/monitor-visually/select-gantt.png)

O comprimento da barra informa a duração do gasoduto. Também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Alertas

Pode levantar alertas sobre métricas suportadas na Data Factory. Selecione   >  **Alertas monitor & métricas** na página de monitorização da Data Factory para começar.

![Página do Monitor da fábrica de dados](media/monitor-visually/start-page.png)

Para uma introdução e demonstração de sete minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **nova regra de alerta** para criar um novo alerta.

    ![Novo botão de regra de alerta](media/monitor-visually/new-alerts.png)

1.  Especifique o nome da regra e selecione a gravidade do alerta.

    ![Caixas para nome de regra e gravidade](media/monitor-visually/name-and-severity.png)

1.  Selecione os critérios de alerta.

    ![Caixa para critérios-alvo](media/monitor-visually/add-criteria-1.png)

    ![Screenshot que mostra onde seleciona uma métrica para configurar a condição de alerta.](media/monitor-visually/add-criteria-2.png)

    ![Lista de critérios](media/monitor-visually/add-criteria-3.png)

    Pode criar alertas em várias métricas, incluindo as de contagem/tamanho de entidade ADF, operações/gasoduto/gatilho, utilização/utilização do CPU de integração (IR) de utilização/memória/contagem/fila de nó, bem como para execuções de pacotes SSIS e operações de arranque/paragem do SSIS IR.

1.  Configurar a lógica de alerta. Pode criar um alerta para a métrica selecionada para todos os oleodutos e atividades correspondentes. Também pode selecionar um determinado tipo de atividade, nome de atividade, nome do gasoduto ou tipo de falha.

    ![Opções para configurar a lógica de alerta](media/monitor-visually/alert-logic.png)

1.  Configure e-mails, SMS, push e notificações de voz para o alerta. Crie um grupo de ação, ou escolha um existente, para as notificações de alerta.

    ![Opções para configurar notificações](media/monitor-visually/configure-notification-1.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/configure-notification-2.png)

1.  Crie a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a monitorização e gestão de gasodutos, consulte o [Monitor e gere os oleodutos programáticamente.](./monitor-programmatically.md)