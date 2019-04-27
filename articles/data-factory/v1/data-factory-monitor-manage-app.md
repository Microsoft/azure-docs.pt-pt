---
title: Monitorizar e gerir pipelines de dados - Azure | Documentos da Microsoft
description: Saiba como utilizar a aplicação de monitorização e gestão para monitorizar e gerir pipelines e as fábricas de dados do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826752"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorizar e gerir pipelines do Azure Data Factory com a aplicação de monitorização e gestão
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Com monitorização e gestão de aplicações](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [monitorizar e gerir pipelines do Data Factory no](../monitor-visually.md).

Este artigo descreve como utilizar a aplicação de monitorização e gestão para monitorizar, gerir e depurar pipelines da fábrica de dados. Pode começar a utilizar com a aplicação por ver o vídeo seguinte:

> [!NOTE]
> A interface do usuário mostrada no vídeo pode corresponder não exatamente o que vê no portal. É um pouco mais antigo, mas os conceitos continuam os mesmos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Iniciar a monitorização e a aplicação de gestão
Para iniciar a aplicação de monitorização e gestão, clique a **monitorizar e gerir** mosaico a **Data Factory** painel fábrica de dados.

![Monitorização do mosaico na home page do Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Deverá ver a aplicação de monitorização e gestão abrir numa janela separada.  

![Aplicação de Monitorização e Gestão](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se vir que o navegador da web é "Autorizar...", desmarque a **bloquear cookies de terceiros e dados do site** caixa de verificação – ou mantê-lo selecionado, crie uma exceção para **login.microsoftonline.com**, e em seguida, tente abrir a aplicação novamente.


Na lista de Windows de atividade no painel do meio, verá uma janela de atividade para cada execução de uma atividade. Por exemplo, se tiver a atividade agendada para ser executada de hora a hora para cinco horas, verá cinco janelas de atividade associadas com cinco setores de dados. Se não vir janelas de atividade na lista na parte inferior, efetue o seguinte:
 
- Atualização do **hora de início** e **hora de fim** filtros na parte superior para corresponder as horas de início e de fim do seu pipeline e, em seguida, clique nas **aplicar** botão.  
- A lista de Windows de atividade não é atualizada automaticamente. Clique no **Atualize** na barra de ferramentas no **atividade Windows** lista.  

Se não tiver uma aplicação de fábrica de dados para testar estes passos com, fazer o tutorial: [copiar dados de armazenamento de BLOBs para base de dados SQL com o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Compreender a monitorização e a aplicação de gestão
Existem três guias à esquerda: **Explorador de recursos**, **vistas de monitorização**, e **alertas**. A primeira guia (**Explorador de recursos**) está selecionada por predefinição.

### <a name="resource-explorer"></a>Explorador de Recursos
Verá o seguinte:

* O Explorador de recursos **vista de árvore** no painel esquerdo.
* O **vista de diagrama** na parte superior no painel do meio.
* O **atividade Windows** lista na parte inferior no painel do meio.
* O **propriedades**, **Explorador de janelas de atividade**, e **Script** separadores no painel da direita.

No Explorador de recursos, pode ver todos os recursos (pipelines, conjuntos de dados, serviços ligados) na fábrica de dados numa vista de árvore. Quando seleciona um objeto no Explorador de recursos:

* A entidade de fábrica de dados associada está realçada na vista de diagrama.
* [Janelas de atividade de associados](data-factory-scheduling-and-execution.md) são realçados na lista de Windows de atividade na parte inferior.  
* As propriedades do objeto selecionado são apresentadas na janela de propriedades no painel da direita.
* A definição de JSON do objeto selecionado é mostrada, se aplicável. Por exemplo: um serviço ligado, um conjunto de dados ou um pipeline.

![Explorador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte a [agendamento e execução](data-factory-scheduling-and-execution.md) artigo para obter informações concetuais detalhadas sobre janelas de atividade.

### <a name="diagram-view"></a>Vista de Diagrama
A vista de diagrama de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir uma fábrica de dados e de seus ativos. Quando seleciona uma entidade de fábrica de dados (conjunto de dados/pipeline) na vista de diagrama:

* A entidade de fábrica de dados é selecionada na vista de árvore.
* As janelas de atividade associadas são realçadas na lista de Windows de atividade.
* As propriedades do objeto selecionado são apresentadas na janela Propriedades.

Quando o pipeline está ativado (não está no Estado em pausa), é mostrado com uma linha verde:

![Execução de pipeline](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Pode colocar em pausa, retomar ou encerrar um pipeline selecionando-o na vista de diagrama e utilizando os botões na barra de comandos.

![Em pausa/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existem três botões da barra de comando para o pipeline na vista de diagrama. Pode utilizar o segundo botão para colocar em pausa o pipeline. Colocar em pausa não terminar as atividades atualmente em execução e permite que eles continuar até à conclusão. O terceiro botão Pausa o pipeline e encerra sua execução de atividades existentes. O primeiro botão retoma o pipeline. Quando o seu pipeline está em pausa, altera a cor do pipeline. Por exemplo, um pipeline em pausa é semelhante na imagem seguinte: 

![Pipeline em pausa](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pode selecionar vários pipelines de duas ou mais, utilizando a tecla Ctrl. Pode utilizar os botões da barra de comando para colocar em pausa/retomar vários pipelines cada vez.

Pode também um pipeline com o botão direito e selecione opções para suspender, retomar ou encerrar um pipeline. 

![Menu de contexto para o pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique nas **abrir pipeline** opção para ver todas as atividades no pipeline. 

![Menu Abrir pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na vista pipeline aberto, verá todas as atividades no pipeline. Neste exemplo, há apenas uma atividade: Atividade de cópia. 

![Pipeline aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para regressar à vista anterior, clique no nome da fábrica de dados no menu de trilho na parte superior.

Na vista de pipeline, quando seleciona um conjunto de dados de saída ou quando move o mouse sobre o conjunto de dados de saída, verá a janela de pop-up de Windows de atividade para esse conjunto de dados.

![Janela de pop-up de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Pode clicar numa janela de atividade para ver detalhes do mesmo no **propriedades** janela no painel da direita.

![Propriedades da janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel da direita, mude para o **Explorador de janelas de atividade** separador para ver mais detalhes.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Consulte também **resolvido variáveis** para cada tentativa de execução de uma atividade no **tentativas** secção.

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mude para o **Script** guia para ver a definição de script JSON para o objeto selecionado.   

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Pode ver as janelas de atividade em três locais:

* O pop-up do Windows de atividade na vista de diagrama (painel central).
* O Explorador de janelas de atividade no painel da direita.
* A lista de Windows de atividade no painel inferior.

No pop-up de atividade Windows e no Explorador de janelas de atividade, pode rolar para a semana anterior e próxima semana, utilizando as setas do esquerda e direita.

![Setas de esquerda/direita do Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da vista de diagrama, verá esses botões: Aplicar zoom no, ampliar horizontalmente, Zoom para ajustar, Zoom de 100%, layout de bloqueio. O **esquema de bloqueio** botão impede a sejam movidas acidentalmente tabelas e pipelines na vista de diagrama. Ele está ativada por predefinição. Pode desativá-la e mover entidades no diagrama. Quando desativá-la, pode utilizar o último botão para posicionar automaticamente pipelines e tabelas. Também pode ampliar ou reduzir com a roda do mouse.

![Comandos de zoom da vista de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Janelas de Atividades
A lista de Windows de atividade na parte inferior do painel do meio apresenta todas as janelas de atividade para o conjunto de dados que selecionou no Explorador de recursos ou a vista de diagrama. Por predefinição, a lista está na ordem decrescente, que significa que verá a janela de atividade mais recente na parte superior.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista não atualiza automaticamente, por isso, utilize o botão de atualização na barra de ferramentas para atualizá-lo manualmente.  

Janelas de atividade podem ter um dos seguintes Estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Substatus</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>ScheduleTime</td><td>O tempo não são fornecidos para a janela de atividade executar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas a executar outras janelas de atividade.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está em pausa e não é possível executar as janelas de atividade até que seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está a ser repetida.</td>
</tr>
<tr>
<td>Validação</td><td>Validação ainda não começou.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Está a aguardar validação seja repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Em Curso</td><td>A Validar</td><td>Validação está em curso.</td>
</tr>
<td>-</td>
<td>A janela de atividade está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Com Falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividade foi cancelada por ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>A janela de atividade não conseguiu ser gerado ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A janela de atividade está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorada</td><td>-</td><td>A janela de atividade não foi processada.</td>
</tr>
<tr>
<td>Nenhuma</td><td>-</td><td>Uma janela de atividade existia com um Estado diferente, mas foi reposta.</td>
</tr>
</table>


Quando clica numa janela de atividade na lista, pode ver detalhes sobre ele na **atividade Windows Explorer** ou o **propriedades** janela à direita.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualização de janelas de atividade
Os detalhes não são atualizados automaticamente, por isso, utilize o botão de atualização (o segundo botão) na barra de comandos para atualizar manualmente a lista de janelas de atividades.  

### <a name="properties-window"></a>Janela de propriedades
A janela de propriedades é no painel mais à direita da aplicação de monitorização e gestão.

![Janela de propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Ele exibe propriedades do item selecionado no Explorador de recursos (vista de árvore), vista de diagrama ou lista de Windows de atividade.

### <a name="activity-window-explorer"></a>Explorador de janelas de atividade
O **Explorador de janelas de atividade** janela é no painel mais à direita da aplicação de monitorização e gestão. Ele apresenta detalhes sobre a janela de atividade que selecionou na janela de pop-up de atividade Windows ou na lista de Windows de atividade.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Pode mudar para outra janela de atividade ao clicar no mesmo na vista de calendário, na parte superior. Também pode utilizar os botões de seta de seta esquerda/direita na parte superior para ver as janelas de atividade da semana anterior ou a semana seguinte.

Pode utilizar os botões da barra de ferramentas no painel inferior para executar novamente a janela de atividade ou atualizar os detalhes no painel.

### <a name="script"></a>Script
Pode utilizar o **Script** separador para ver a definição de JSON da entidade de fábrica de dados selecionada (serviço ligado, conjunto de dados ou pipeline).

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Utilizar vistas de sistema
A aplicação de monitorização e gestão inclui vistas de sistema previamente criados (**janelas de atividade recente**, **falha janelas de atividade**, **janelas de atividade em curso**) que permitem Para ver as janelas de atividade recente/falhou/em curso para a fábrica de dados.

Mude para o **vistas de monitorização** separador à esquerda ao clicar no mesmo.

![Separador de vistas de monitorização](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, existem três modos de exibição do sistema que são suportados. Selecione uma opção para ver as janelas de atividades recentes, janelas de atividade falhado ou janelas de atividade de em curso na lista de Windows de atividade (na parte inferior do painel do meio).

Quando seleciona a **janelas de atividade recente** opção, verá todas as janelas de atividade recente por ordem descendente da **tentar pela última vez**.

Pode utilizar o **falha janelas de atividade** vista para ver todas as janelas de atividade falhada na lista. Selecione uma janela de atividade falhada na lista para ver detalhes sobre ele na **propriedades** janela ou o **Explorador de janelas de atividade**. Também pode transferir quaisquer registos para uma janela de atividade falhada.

## <a name="sort-and-filter-activity-windows"></a>Ordenar e filtrar janelas de atividade
Alteração da **hora de início** e **hora de fim** definições na barra de comando para janelas de atividade de filtro. Depois de alterar a hora de início e hora de fim, clique no botão junto à hora de fim para atualizar a lista de Windows de atividade.

![Horas de início e fim](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todos os tempos são em formato UTC em que a aplicação de monitorização e gestão.
>
>

Na **lista de atividade Windows**, clique no nome de uma coluna (por exemplo: Status).

![Menu de coluna de lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Pode fazer o seguinte:

* Ordenar por ordem ascendente.
* Classificar em ordem decrescente.
* Filtrar por um ou mais valores (pronto, espera e assim por diante).

Quando especificar um filtro numa coluna, verá o botão de filtro ativado para essa coluna, que indica que os valores na coluna são valores filtrados.

![Filtrar numa coluna de lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pode utilizar a mesma janela de pop-up para limpar filtros. Para limpar todos os filtros para obter a lista de Windows de atividade, clique no botão Limpar filtro na barra de comandos.

![Limpar todos os filtros para obter a lista de Windows de atividade](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Executar ações de batch
### <a name="rerun-selected-activity-windows"></a>Volte a executar o windows de atividade selecionada
Selecione uma janela de atividade, clique na seta para baixo para o primeiro botão de barra de comando e selecione **volte a executar** / **executar novamente com a montante no pipeline**. Quando seleciona a **volte a executar com a montante no pipeline** opção, ele volta a executar todas as janelas de atividade a montante também.
    ![Volte a executar uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também pode selecionar várias janelas de atividade na lista e executá-los ao mesmo tempo. Pode querer filtrar com base no status de janelas de atividade (por exemplo: **Falha ao**) – e, em seguida, volte a executar os windows de actividade falhada depois de corrigir o problema que faz com que as janelas de atividade efetuar a ativação. Consulte a secção seguinte para obter detalhes sobre a filtragem de janelas de atividade na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Em pausa/retomar vários pipelines
Pode é necessário um dois ou mais pipelines de seleção múltipla, utilizando a tecla Ctrl. Pode utilizar os botões da barra de comando (que estão realçados no retângulo vermelho na imagem seguinte) para colocar em pausa/retomá-los.

![Em pausa/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
