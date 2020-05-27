---
title: Monitorizar visualmente o Azure Data Factory
description: Saiba como monitorizar visualmente as fábricas de dados do Azure
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 44a95430ece9c55bb088ae5b968c33691f25b4c5
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860835"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorizar visualmente o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Depois de ter criado e publicado um oleoduto na Azure Data Factory, pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Pode monitorizar todas as suas operações de pipeline de forma nativa na experiência do utilizador da Azure Data Factory. Para abrir a experiência de monitorização, selecione o **Monitor & Gerir** o azulejo na lâmina da fábrica de dados do portal [Azure](https://portal.azure.com/). Se já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda.

Todas as operações de fábrica de dados são exibidas no fuso horário local do navegador. Se alterar o fuso horário, todos os campos de data/hora encaixem no que selecionou.

## <a name="monitor-pipeline-runs"></a>Monitorizar execuções de pipeline

A vista de monitorização predefinida é a lista de funcionamento do gasoduto no período de tempo selecionado. São apresentadas as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do oleoduto | Nome do oleoduto |
| Ações | Ícones que lhe permitem ver detalhes de atividade, cancelar ou reexecutar o pipeline |
| Início de execução | Data e hora de início para a execução do gasoduto (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Desencadeado por | O nome do gatilho que iniciou o oleoduto |
| Estado | **Falhado,** **Sucedido**, **Em Progresso,** **Cancelado,** ou **Fila** |
| Anotações | Etiquetas filtradas associadas a um gasoduto  |
| Parâmetros | Parâmetros para a execução do gasoduto (pares de nome/valor) |
| Erro | Se o gasoduto falhou, o erro de execução |
| o ID da Execução | ID da execução do gasoduto |

![Vista de lista para monitorização de gasodutos](media/monitor-visually/pipeline-runs.png)

É necessário selecionar manualmente o botão **Refresh** para atualizar a lista de gasodutos e funcionamento de atividade. A atualização automática não é suportada atualmente.

![Botão de atualização](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorização de execuções de atividade

Para ver as corridas de atividade para cada execução do gasoduto, selecione a **atividade do View executa** o ícone sob a coluna **Ações.** A vista da lista mostra que a atividade funciona que corresponde a cada execução de gasoduto.

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da Atividade | Nome da atividade dentro do oleoduto |
| Tipo de Atividade | Tipo de atividade, tais como **Copy,** **ExecuteDataFlow,** ou **AzureMLExecutePipeline** |
| Ações | Ícones que lhe permitem ver informações de entrada jSON, informações de saída jSON ou experiências detalhadas de monitorização específicas da atividade | 
| Início de execução | Data e hora de início para a execução da atividade (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Estado | **Falhado,** **Bem sucedido,** **Em Progresso**, ou **Cancelado** |
| Tempo de execução de integração | Qual Integração Tempo de Execução a atividade foi executado em |
| Propriedades do Utilizador | Propriedades definidas pelo utilizador da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| o ID da Execução | ID da execução de atividade |

![Vista de lista para monitorização de executantes de atividades](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Promover propriedades dos utilizadores para monitorizar

Promova qualquer propriedade de atividade de pipeline como propriedade do utilizador para que se torne uma entidade que monitoriza. Por exemplo, pode promover as propriedades **Fonte** e **Destino** da atividade de cópia no seu pipeline como propriedades do utilizador. Selecione **Auto Generate** para gerar as propriedades do utilizador **Fonte** e **Destino** para uma atividade de cópia.

![Criar propriedades do utilizador](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Só pode promover até cinco propriedades de atividade de pipeline como propriedades do utilizador.

Depois de criar as propriedades do utilizador, pode monitorizá-las nas vistas da lista de monitorização. Se a fonte para a atividade de cópia for um nome de mesa, pode monitorizar o nome da tabela de origem como uma coluna na vista da lista para as execuções de atividade.

![A lista de executações de atividade sem propriedades do utilizador](media/monitor-visually/monitor-user-properties-image2.png)

![Adicione colunas para propriedades do utilizador na lista de executantes de atividade](media/monitor-visually/monitor-user-properties-image3.png)

![A lista de executa a atividade com colunas para propriedades do utilizador](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configure a vista da lista

### <a name="order-and-filter"></a>Encomenda e filtro

Altere se as condutas de gasoduto saem em descida ou ascendente de acordo com a hora de início da corrida. O gasoduto de filtro funciona utilizando as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do oleoduto | Filtre pelo nome do oleoduto. |
| Início de execução |  Determine o intervalo de tempo do gasoduto exposto. As opções incluem filtros rápidos para **as últimas 24 horas,** **na semana passada,** e **últimos 30 dias** ou para selecionar uma data e hora personalizadas. |
| Estado de execução | O filtro corre por estado: **Bem sucedido,** **falhado,** **fila,** **cancelado**ou **em curso**. |
| Anotações | Filtro por etiquetas aplicadas a cada pipeline |
| Execuções | Filtre se quer ver gasodutos recorridos |

![Opções para filtragem](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique no cabeçalho da visualização da lista e escolha colunas que pretende aparecer na vista da lista.

![Opções para colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar as larguras das colunas
Aumente e diminua as larguras da coluna na vista da lista pairando sobre o cabeçalho da coluna.

## <a name="rerun-activities-inside-a-pipeline"></a>Reexecutar atividades dentro de um oleoduto

Pode refazer atividades dentro de um oleoduto. Selecione **a atividade do View**e, em seguida, selecione a atividade no seu pipeline a partir do qual pretende reproduzir o seu pipeline.

![Ver execuções de atividades](media/monitor-visually/rerun-activities-image1.png)

![Selecione uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Reexecutar da atividade falhada

Se uma atividade falhar, vezes fora ou for cancelada, pode reexecutar o gasoduto a partir dessa atividade falhada selecionando **Rerun de atividade falhada**.

![Reexecutar atividade falhada](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Ver história de rerun

Pode ver a história da reexecução para todos os pipelines que correm na vista da lista.

![Ver histórico](media/monitor-visually/rerun-history-image1.png)

Você também pode ver a história de recorrer para uma determinada execução de gasoduto.

![Ver história para uma corrida de gasodutos](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorizar o consumo

Pode ver os recursos consumidos por um gasoduto executado clicando no ícone de consumo ao lado da corrida. 

![Monitorizar o consumo](media/monitor-visually/monitor-consumption-1.png)

Clicar no ícone abre um relatório de consumo dos recursos utilizados por esse gasoduto. 

![Monitorizar o consumo](media/monitor-visually/monitor-consumption-2.png)

Pode ligar estes valores à calculadora de [preços Azure](https://azure.microsoft.com/pricing/details/data-factory/) para estimar o custo da execução do gasoduto. Para obter mais informações sobre os preços da Fábrica de Dados Azure, consulte [a compreensão dos preços.](pricing-concepts.md)

> [!NOTE]
> Estes valores devolvidos pela calculadora de preços são uma estimativa. Não reflete o valor exato que será cobrado pela Azure Data Factory 

## <a name="gantt-views"></a>Vistas de Gantt

Use vistas gantt para visualizar rapidamente os seus oleodutos e corridas de atividade.

![Exemplo de um gráfico de Gantt](media/monitor-visually/gantt1.png)

Você pode olhar para a vista Gantt por oleoduto ou grupo por anotações/tags que você criou nos seus oleodutos.

![Anotações de gráficos de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do gasoduto. Também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Visitas guiadas
Selecione o ícone **Informação** na parte inferior esquerda. Em seguida, selecione **Guided Tours** para obter instruções passo a passo sobre como monitorizar o seu pipeline e funciona a atividade.

![Visitas guiadas](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alertas

Pode levantar alertas sobre métricas suportadas na Data Factory. Selecione **Monitor**  >  **Alerts & métricas** na página de monitorização da Fábrica de Dados para começar.

![Página de Monitor de fábrica de dados](media/monitor-visually/start-page.png)

Para uma introdução de sete minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar alertas

1.  Selecione **Nova regra de alerta** para criar um novo alerta.

    ![Novo botão de regra de alerta](media/monitor-visually/new-alerts.png)

1.  Especifique o nome da regra e selecione a gravidade do alerta.

    ![Caixas para nome de regra e gravidade](media/monitor-visually/name-and-severity.png)

1.  Selecione os critérios de alerta.

    ![Caixa para critérios-alvo](media/monitor-visually/add-criteria-1.png)

    ![Lista de critérios](media/monitor-visually/add-criteria-2.png)

1.  Configure a lógica de alerta. Pode criar um alerta para a métrica selecionada para todos os oleodutos e atividades correspondentes. Também pode selecionar um determinado tipo de atividade, nome de atividade, nome do pipeline ou tipo de falha.

    ![Opções para configurar lógica de alerta](media/monitor-visually/alert-logic.png)

1.  Configure e-mails, SMS, push e notificações de voz para o alerta. Crie um grupo de ação, ou escolha um existente, para as notificações de alerta.

    ![Opções para configurar notificações](media/monitor-visually/configure-notification-1.png)

    ![Opções para adicionar uma notificação](media/monitor-visually/configure-notification-2.png)

1.  Crie a regra de alerta.

    ![Opções para criar uma regra de alerta](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre monitorização e gestão de gasodutos, consulte o Monitor e gere o artigo [de pipelines programáticamente.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
