---
title: Monitorar visualmente data factories do Azure | Microsoft Docs
description: Saiba como monitorar visualmente as fábricas de dados do Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720608"
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorar visualmente data factories do Azure
O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI).

Neste guia de início rápido, você aprende a monitorar visualmente Data Factory pipelines sem escrever uma única linha de código.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="monitor-data-factory-pipelines"></a>Monitorar Data Factory pipelines

Monitore execuções de pipeline e de atividade com uma interface de exibição de lista simples. Todas as execuções são exibidas no fuso horário do navegador local. Você pode alterar o fuso horário e todos os campos de data e hora se ajustam ao fuso horário selecionado.  

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. Faça logon no [portal do Azure](https://portal.azure.com/).
3. Navegue até a folha data factory criada em portal do Azure e clique no bloco ' monitorar & Gerenciar ' para iniciar a experiência de monitoramento do Data Factory Visual.

## <a name="monitor-pipeline-runs"></a>Monitorizar execuções de pipeline
Vista de lista que mostra cada execução de pipeline dos seus pipelines da fábrica de dados v2. Colunas incluídas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do pipeline | Nome do pipeline. |
| Ações | Ação única disponível para exibir as execuções de atividade. |
| Iniciar execução | Data e hora de início da execução do pipeline (MM/DD/AAAA, HH: MM: SS AM/PM) |
| Duration | Duração da execução (HH: MM: SS) |
| Acionada Por | Gatilho manual, gatilho de agendamento |
| Estado | Com falha, com êxito, em andamento |
| Parâmetros | Parâmetros de execução de pipeline (pares nome, valor) |
| Erro | Erro de execução de pipeline (if/any) |
| ID de Execução | ID da execução do pipeline |

![Monitorizar execuções de pipeline](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorização de execuções de atividade
Vista de lista que mostra as execuções de atividades que correspondem a cada execução de pipeline. Clique no ícone **' execuções de atividade '** na coluna **' ações '** para exibir as execuções de atividade para cada execução de pipeline. Colunas incluídas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome da Actividade | Nome da atividade dentro do pipeline. |
| Tipo de Atividade | Tipo de atividade, como Copy, HDInsightSpark, HDInsightHive, etc. |
| Iniciar execução | Data e hora de início da execução da atividade (MM/DD/AAAA, HH: MM: SS AM/PM) |
| Duration | Duração da execução (HH: MM: SS) |
| Estado | Com falha, com êxito, em andamento |
| Input | Matriz JSON que descreve as entradas de atividade |
| Output | Matriz JSON que descreve as saídas de atividade |
| Erro | Erro de execução da atividade (if/any) |

![Monitorização de execuções de atividade](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Você precisa clicar no ícone **' Atualizar '** na parte superior para atualizar a lista de execuções de pipeline e de atividade. A atualização automática não é suportada atualmente.

![Atualizar](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Selecione um data factory para monitorar
Focalize o ícone de **Data Factory** na parte superior esquerda. Clique no ícone ' seta ' para ver uma lista de assinaturas e fábricas de dados do Azure que você pode monitorar.

![Selecionar fábrica de dados](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Configurar o modo de exibição de lista

### <a name="apply-rich-ordering-and-filtering"></a>Aplicar ordenação e filtragem ricas

Ordenar execuções de pipeline em desc/ASC por executar iniciar e filtrar execuções de pipeline pelas seguintes colunas:

| **Nome da coluna** | **Descrição** |
| --- | --- |
| Nome do pipeline | Nome do pipeline. As opções incluem filtros rápidos para ' últimas 24 horas ', ' última semana ', ' últimos 30 dias ' ou selecione uma data e hora personalizada. |
| Iniciar execução | Data e hora de início da execução do pipeline |
| Status da execução | Filtros executados por status-com êxito, falharam, em andamento |

![Filtro](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Adicionar ou remover colunas
Clique com o botão direito do mouse no cabeçalho da exibição de lista e escolha as colunas que você deseja exibir na exibição de lista

![Colunas](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Ajustar larguras de coluna
Aumentar e diminuir as larguras de coluna no modo de exibição de lista passando o mouse sobre o cabeçalho da coluna

## <a name="promote-user-properties-to-monitor"></a>Promover Propriedades do usuário para monitorar

Você pode promover qualquer propriedade de atividade de pipeline como uma propriedade de usuário para que ela se torne uma entidade que você pode monitorar. Por exemplo, você pode promover as propriedades de **origem** e **destino** da atividade de cópia em seu pipeline como propriedades do usuário. Você também pode selecionar **gerar automaticamente** para gerar as propriedades de usuário de **origem** e de **destino** para uma atividade de cópia.

![Criar propriedades do usuário](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Você só pode promover até cinco Propriedades de atividade de pipeline como propriedades do usuário.

Depois de criar as propriedades do usuário, você pode monitorá-las nos modos de exibição da lista de monitoramento. Se a origem da atividade de cópia for um nome de tabela, você poderá monitorar o nome da tabela de origem como uma coluna na exibição de lista de execuções de atividade.

![Lista de execuções de atividades sem propriedades do usuário](media/monitor-visually/monitor-user-properties-image2.png)

![Adicionar colunas para propriedades do usuário à lista de execuções de atividade](media/monitor-visually/monitor-user-properties-image3.png)

![Lista de execuções de atividade com colunas para propriedades de usuário](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Executar atividades novamente dentro de um pipeline

Agora você pode executar novamente as atividades dentro de um pipeline. Clique em **Exibir execuções de atividade** e selecione a atividade no pipeline da qual ponto você deseja executar novamente o pipeline.

![Ver execuções de atividades](media/monitor-visually/rerun-activities-image1.png)

![Selecionar uma execução de atividade](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Exibir o histórico de reexecução

Você pode exibir o histórico de reexecução para todas as execuções de pipeline no modo de exibição de lista.

![Ver histórico](media/monitor-visually/rerun-history-image1.png)

Você também pode exibir o histórico de reexecução para uma execução de pipeline específica.

![Exibir o histórico de uma execução de pipeline](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Exibições de Gantt

Use exibições de Gantt para visualizar rapidamente os pipelines e as execuções de atividade. Você pode examinar o modo de exibição de Gantt por pipeline ou por anotações/marcas que você criou em seus pipelines.

![Gráfico de Gantt](media/monitor-visually/gantt1.png)

![Anotações do gráfico de Gantt](media/monitor-visually/gantt2.png)

O comprimento da barra informa a duração do pipeline. Você também pode clicar na barra para ver mais detalhes.

![Duração do gráfico de Gantt](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Tours guiados
Clique no ' ícone de informações ' na parte inferior esquerda e clique em ' Tours guiados ' para obter instruções passo a passo sobre como monitorar suas execuções de pipeline e de atividade.

![Tours guiados](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Comentários
Clique no ícone ' comentários ' para nos enviar comentários sobre vários recursos ou sobre quaisquer problemas que você possa estar enfrentando.

![Comentários](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alertas

Você pode gerar alertas sobre as métricas com suporte no Data Factory. Selecione **monitorar > alertas de & métricas** na página do monitor de data Factory para começar.

![](media/monitor-visually/alerts01.png)

Para uma introdução e uma demonstração desse recurso de sete minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Criar Alertas

1.  Clique em **nova regra** de alerta para criar um novo alerta.

    ![](media/monitor-visually/alerts02.png)

1.  Especifique o nome da regra e selecione a **severidade**do alerta.

    ![](media/monitor-visually/alerts03.png)

1.  Selecione os critérios de alerta.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Configure a lógica de alerta. Você pode criar um alerta para a métrica selecionada para todos os pipelines e atividades correspondentes. Você também pode selecionar um tipo específico de atividade, nome da atividade, nome do pipeline ou um tipo de falha.

    ![](media/monitor-visually/alerts06.png)

1.  Configure notificações por **email/SMS/Push/voz** para o alerta. Crie ou escolha um **grupo de ações** existente para as notificações de alerta.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Crie a regra de alerta.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Passos Seguintes

Confira o artigo [monitorar e gerenciar pipelines](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) programaticamente para saber mais sobre como monitorar e gerenciar pipelines.
