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
ms.date: 06/30/2020
ms.openlocfilehash: fcbf48081484941fd93f209bee21eb14c2c38310
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570131"
---
# <a name="visually-monitor-azure-data-factory"></a>Monitorizar visualmente o Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uma vez criado e publicado um oleoduto na Azure Data Factory, pode associá-lo a um gatilho ou iniciar manualmente uma corrida ad hoc. Pode monitorizar todo o seu oleoduto funciona de forma nativa na experiência do utilizador da Azure Data Factory. Para abrir a experiência de monitorização, selecione o **telha Monitor & Gerir** na lâmina de fábrica de dados do [portal Azure](https://portal.azure.com/). Se já estiver no ADF UX, clique no ícone **Monitor** na barra lateral esquerda.

Todas as operações da fábrica de dados são exibidas no fuso horário local do navegador. Se alterar o fuso horário, todos os campos de data/hora encaixam-se no que selecionou.

## <a name="monitor-pipeline-runs"></a>Monitorizar execuções de pipeline

A visão de monitorização predefinida é a lista de gasodutos executados no período de tempo selecionado. São apresentadas as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do pipeline | Nome do oleoduto |
| Ações | Ícones que lhe permitem ver detalhes da atividade, cancelar ou refazer o pipeline |
| Início de execução | Data e hora de início para a execução do gasoduto (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Desencadeado por | O nome do gatilho que iniciou o oleoduto |
| Estado | **Falhado,** **Bem sucedido,** **Em Curso,** **Cancelado,** ou **Em Fila** |
| Anotações | Etiquetas filtradas associadas a um gasoduto  |
| Parâmetros | Parâmetros para a execução do gasoduto (pares de nome/valor) |
| Erro | Se o gasoduto falhou, o erro de execução |
| o ID da Execução | ID da corrida do gasoduto |

![Vista da lista para monitorização de gasodutos](media/monitor-visually/pipeline-runs.png)

É necessário selecionar manualmente o botão **Refresh** para atualizar a lista de gasodutos e de execuções de atividade. A autorefresh não está atualmente apoiada.

![Botão Atualizar](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>Monitorização de execuções de atividade

Para visualizar as operações executadas para cada corrida de gasoduto, selecione o ícone **de atividade 'Ver' corre** sob a coluna **Ações.** A visualização da lista mostra que as operações correspondem a cada corrida de gasodutos.

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da Atividade | Nome da atividade dentro do oleoduto |
| Tipo de Atividade | Tipo de atividade, como **Copy,** **ExecuteDataFlow,** ou **AzureMLExecutePipeline** |
| Ações | Ícones que lhe permitem ver informações de entrada JSON, informações de saída JSON ou experiências de monitorização detalhadas específicas da atividade | 
| Início de execução | Data e hora de início da atividade executada (MM/DD/YYYY, HH:MM:SS AM/PM) |
| Duração | Duração da execução (HH:MM:SS) |
| Estado | **Falhado,** **bem sucedido,** **em curso,** ou **cancelado** |
| Tempo de execução de integração | Qual integração Runtime a atividade foi executado em |
| Propriedades do Utilizador | Propriedades definidas pelo utilizador da atividade |
| Erro | Se a atividade falhou, o erro de execução |
| o ID da Execução | ID da atividade executado |

![A vista da lista para monitorização da atividade corre](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>Promover propriedades de utilizadores para monitorizar

Promover qualquer propriedade de atividade de pipeline como propriedade do utilizador para que se torne uma entidade que você monitorize. Por exemplo, pode promover as propriedades **de Origem** e **Destino** da atividade de cópia no seu pipeline como propriedades do utilizador. Selecione **Auto Generate** para gerar as propriedades do utilizador **Source** and **Destination** para uma atividade de cópia.

![Criar propriedades de utilizador](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Só é possível promover até cinco propriedades de atividade de gasoduto como propriedades do utilizador.

Depois de criar as propriedades do utilizador, pode monitorá-las nas vistas da lista de monitorização. Se a fonte para a atividade da cópia for um nome de mesa, pode monitorizar o nome da tabela de origem como uma coluna na vista da lista para a atividade.

![A atividade executa a lista sem propriedades do utilizador](media/monitor-visually/monitor-user-properties-image2.png)

![Adicione colunas para propriedades de utilizador à lista de execuções de atividades](media/monitor-visually/monitor-user-properties-image3.png)

![A atividade executa a lista com colunas para propriedades do utilizador](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>Configurar a vista da lista

### <a name="order-and-filter"></a>Encomendar e filtrar

Alternar se o gasoduto funciona em descida ou subida de acordo com a hora de início de funcionação. O gasoduto do filtro funciona utilizando as seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do pipeline | Filtrar pelo nome do oleoduto. |
| Início de execução |  Determine o intervalo de tempo dos cursos de conduta visualizados. As opções incluem filtros rápidos para **as últimas 24 horas**, **última semana,** e **últimos 30 dias** ou para selecionar uma data e hora personalizadas. |
| Estado de execução | Filtro funciona por estado: **Bem sucedido,** **Falhado,** **Em Fila,** **Cancelado**ou **Em Curso**. |
| Anotações | Filtro por etiquetas aplicadas a cada oleoduto |
| Execuções | Filtre se deseja ver os oleodutos reran |

![Opções de filtragem](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito no cabeçalho da lista e escolha as colunas que pretende aparecer na vista da lista.

![Opções para colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar larguras de coluna
Aumente e diminua as larguras da coluna na vista da lista, pairando sobre o cabeçalho da coluna.

## <a name="rerun-activities-inside-a-pipeline"></a>Repercutam atividades dentro de um oleoduto

Pode refazer atividades dentro de um oleoduto. Selecione **A atividade de 'Ver' é executado**e, em seguida, selecione a atividade no seu pipeline a partir do qual pretende voltar a fazer a sua tubagem.

![Ver execuções de atividades](media/monitor-visually/rerun-activities-image1.png)

![Selecione uma atividade executada](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>Repetição da atividade falhada

Se uma atividade falhar, o tempo fora ou for cancelada, pode voltar a fazer o pipeline a partir dessa atividade falhada selecionando **a Repetição da atividade falhada**.

![Rerun atividade falhada](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Ver refazer a história

Pode ver o histórico de repetição de todas as correções de pipeline na vista da lista.

![Ver histórico](media/monitor-visually/rerun-history-image1.png)

Também pode ver a história da repetição para uma determinada corrida de gasodutos.

![Ver história para uma corrida de gasodutos](media/monitor-visually/rerun-history-image2.png)

## <a name="monitor-consumption"></a>Monitorizar o consumo

Pode ver os recursos consumidos por um oleoduto executado clicando no ícone de consumo ao lado da corrida. 

![Screenshot que mostra onde você pode ver os recursos consumidos por um oleoduto.](media/monitor-visually/monitor-consumption-1.png)

Clicar no ícone abre um relatório de consumo dos recursos utilizados por esse gasoduto. 

![Monitorizar o consumo](media/monitor-visually/monitor-consumption-2.png)

Pode ligar estes valores à [calculadora de preços Azure](https://azure.microsoft.com/pricing/details/data-factory/) para estimar o custo do gasoduto. Para obter mais informações sobre os preços da Azure Data Factory, consulte [os preços de Compreensão](pricing-concepts.md).

> [!NOTE]
> Estes valores devolvidos pela calculadora de preços são uma estimativa. Não reflete a quantia exata que será faturada pela Azure Data Factory 

## <a name="gantt-views"></a>Vistas de Gantt

Utilize vistas gantt para visualizar rapidamente os seus oleodutos e operações.

![Exemplo de um gráfico de Gantt](media/monitor-visually/gantt1.png)

Pode olhar para a vista Gantt por oleoduto ou grupo através de anotações/tags que criou nos seus oleodutos.

![Anotações de gráfico de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do gasoduto. Também pode selecionar a barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Visitas guiadas
Selecione o ícone **Informação** no lado inferior esquerdo. Em seguida, **selecione Visitas Guiadas** para obter instruções passo a passo sobre como monitorizar o seu oleoduto e a sua atividade.

![Visitas guiadas](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>Alertas

Pode levantar alertas sobre métricas suportadas na Data Factory. Selecione **Monitor**  >  **Alertas monitor & métricas** na página de monitorização da Data Factory para começar.

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

Para aprender sobre a monitorização e gestão de gasodutos, consulte o [Monitor e gere os oleodutos programáticamente.](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)
