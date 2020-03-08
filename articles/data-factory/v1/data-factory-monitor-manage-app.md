---
title: Monitorizar e gerir os gasodutos de dados - Azure
description: Saiba como usar a app de Monitorização e Gestão para monitorizar e gerir fábricas e oleodutos de dados Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384796"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorize e gerencie os oleodutos Azure Data Factory utilizando a app de monitorização e gestão
> [!div class="op_single_selector"]
> * [Usando o portal Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Aplicação de Monitorização e Gestão](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [monitorizar e gerir os gasodutos data Factory em](../monitor-visually.md).

Este artigo descreve como usar a app de Monitorização e Gestão para monitorizar, gerir e desinserir os seus oleodutos Data Factory. Pode começar a usar a aplicação assistindo ao seguinte vídeo:

> [!NOTE]
> A interface de utilizador mostrada no vídeo pode não corresponder exatamente ao que se vê no portal. É um pouco mais velho, mas os conceitos permanecem os mesmos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Lançar a app de Monitorização e Gestão
Para lançar a aplicação Monitor and Management, clique no **monitor & Manage** azulejo na lâmina **data Factory** para a sua fábrica de dados.

![Telha de monitorização na página inicial da Fábrica de Dados](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Deve ver a aplicação de Monitorização e Gestão aberta numa janela separada.  

![Aplicação de Monitorização e Gestão](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se vir que o navegador está preso em "Autorizar...", limpe os **cookies de terceiros** do Bloco e verifique os dados do site -- ou mantenha-o selecionado, crie uma exceção para **login.microsoftonline.com**, e tente abrir novamente a app.


Na lista do Windows de Atividade sintetizadora no painel do meio, vê-se uma janela de atividade para cada execução de uma atividade. Por exemplo, se tiver a atividade programada para funcionar de hora a hora durante cinco horas, vê cinco janelas de atividade associadas a cinco fatias de dados. Se não vir janelas de atividade na lista na parte inferior, faça o seguinte:
 
- Atualize os filtros de hora de **início** e **de fim** na parte superior para corresponder aos tempos de início e de fim do seu pipeline e, em seguida, clique no botão **Aplicar.**  
- A lista de Janelas de Atividades não é automaticamente atualizada. Clique no botão **Refresh** na barra de ferramentas na lista do Windows de **Atividade.**  

Se não tiver uma aplicação data Factory para testar estes passos, faça o tutorial: copiar dados do Blob Storage para a Base de [Dados SQL utilizando](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)data factory .

## <a name="understand-the-monitoring-and-management-app"></a>Compreender a aplicação de Monitorização e Gestão
Existem três separadores à esquerda: **Explorador de Recursos,** **Vistas de Monitorização**e **Alertas**. O primeiro separador **(Explorador de Recursos)** é selecionado por defeito.

### <a name="resource-explorer"></a>Explorador de Recursos
Veja o seguinte:

* A **vista da árvore** do Explorador de Recursos no painel esquerdo.
* A **vista do diagrama** na parte superior no painel do meio.
* A lista **do Windows de Atividade** na parte inferior do painel do meio.
* As **propriedades,** **explorador de janelas**de atividade e separadores **script** no painel direito.

No Resource Explorer, você vê todos os recursos (oleodutos, conjuntos de dados, serviços ligados) na fábrica de dados em vista de árvores. Quando selecionar um objeto no Explorador de Recursos:

* A entidade associada da Fábrica de Dados está em destaque na Visão do Diagrama.
* [As janelas](data-factory-scheduling-and-execution.md) de atividade associadas são destacadas na lista de Janelas de Atividade seleções na parte inferior.  
* As propriedades do objeto selecionado são mostradas na janela Propriedades no painel direito.
* A definição JSON do objeto selecionado é mostrada, se aplicável. Por exemplo: um serviço ligado, um conjunto de dados ou um pipeline.

![Explorador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo de [Agendamento e Execução](data-factory-scheduling-and-execution.md) para obter informações conceptuais detalhadas sobre janelas de atividade.

### <a name="diagram-view"></a>Vista de Diagrama
A Visão do Diagrama de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir uma fábrica de dados e seus ativos. Quando selecionar uma entidade data factory (dataset/pipeline) na vista do diagrama:

* A entidade da fábrica de dados é selecionada na vista da árvore.
* As janelas de atividade associadas estão em destaque na lista do Windows de Atividades.
* As propriedades do objeto selecionado são mostradas na janela Propriedades.

Quando o gasoduto está ativado (não em estado de pausa), é mostrado com uma linha verde:

![Gasoduto em execução](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Pode parar, retomar ou encerrar um gasoduto selecionando-o na vista do diagrama e utilizando os botões na barra de comando.

![Pausa/retoma na barra de comando](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existem três botões de barra de comando para o oleoduto na vista do diagrama. Pode utilizar o segundo botão para parar o gasoduto. A pausa não termina as atividades atualmente em curso e permite-lhes proceder à conclusão. O terceiro botão interrompe o gasoduto e encerra as suas atividades de execução existentes. O primeiro botão retoma o gasoduto. Quando o seu gasoduto é interrompido, a cor do gasoduto muda. Por exemplo, um gasoduto pausado parece na seguinte imagem: 

![Gasoduto pausado](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pode selecionar vários oleodutos utilizando a tecla CTRL. Pode utilizar os botões da barra de comando para parar/retomar vários oleodutos de cada vez.

Também pode clicar num pipeline e selecionar opções para suspender, retomar ou encerrar um pipeline. 

![Menu de contexto para pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique na opção **de pipeline Open** para ver todas as atividades no pipeline. 

![Menu Abrir pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na vista aberta do oleoduto, você vê todas as atividades no oleoduto. Neste exemplo, há apenas uma atividade: Copy Activity. 

![Oleoduto aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para voltar à vista anterior, clique no nome da fábrica de dados no menu breadcrumb no topo.

Na vista do pipeline, quando seleciona um conjunto de dados de saída ou quando move o rato sobre o conjunto de dados de saída, vê a janela pop-up do Activity Windows para esse conjunto de dados.

![Janela pop-up do Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Pode clicar numa janela de atividade para ver detalhes na janela **Propriedades** no painel certo.

![Propriedades da janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel certo, mude para o separador **Activity Window Explorer** para ver mais detalhes.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Você também vê **variáveis resolvidas** para cada tentativa de execução para uma atividade na secção **Tentativas.**

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mude para o separador **Script** para ver a definição de script JSON para o objeto selecionado.   

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Você pode ver janelas de atividade em três lugares:

* O Pop-up do Windows de Atividade na Vista do Diagrama (painel do meio).
* O Explorador da Janela de Atividade no painel direito.
* A lista do Windows de Atividade sintetiza no painel inferior.

No pop-up do Activity Windows e no Activity Window Explorer, pode deslocar-se para a semana anterior e para a semana seguinte utilizando as setas esquerda e direita.

![Janela de atividade Explorador setas esquerda/direita](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da vista do diagrama, você vê estes botões: Zoom In, Zoom out, Zoom to Fit, Zoom 100%, Layout de bloqueio. O botão **de disposição 'Lock'** impede-o de mover acidentalmente mesas e oleodutos na vista do diagrama. Está ligado por defeito. Pode desligá-lo e mover entidades no diagrama. Quando o desligar, pode utilizar o último botão para posicionar automaticamente as tabelas e os gasodutos. Também pode fazer zoom para dentro ou para fora utilizando a roda do rato.

![Diagrama Ver comandos de zoom](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Janelas de Atividades
A lista do Windows de Atividade na parte inferior do painel médio apresenta todas as janelas de atividade para o conjunto de dados que selecionou no Explorer de Recursos ou na Visão do Diagrama. Por padrão, a lista está em ordem descendente, o que significa que você vê a janela de atividade mais recente no topo.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista não se atualiza automaticamente, por isso utilize o botão de atualização na barra de ferramentas para o refrescar manualmente.  

As janelas de atividade podem estar num dos seguintes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestatuto</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>Horário de tempo</td><td>Não chegou a hora da janela de atividade sacá-lo.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos da computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todos os casos de atividade estão ocupados a executar outras janelas de atividade.</td>
</tr>
<tr>
<td>AtividadeCurrículo</td><td>A atividade é interrompida e não pode executar as janelas de atividade até que seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está a ser novamente tentada.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não começou.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A validação está à espera de ser novamente tentada.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Em curso</td><td>Validação</td><td>A validação está em andamento.</td>
</tr>
<td>-</td>
<td>A janela de atividade está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Falhou</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividade foi cancelada por ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>A janela de atividade não foi gerada ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A janela de atividade está pronta para ser reemitada.</td>
</tr>
<tr>
<td>Ignorado</td><td>-</td><td>A janela de atividade não foi processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma janela de atividade costumava existir com um estado diferente, mas foi redefinida.</td>
</tr>
</table>


Ao clicar numa janela de atividade na lista, vê detalhes sobre o mesmo no **Explorer do Windows de Atividade** ou na janela **Propriedades** à direita.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar janelas de atividade
Os detalhes não são automaticamente atualizados, por isso use o botão de atualização (o segundo botão) na barra de comando para refrescar manualmente a lista de janelas de atividade.  

### <a name="properties-window"></a>Janela de propriedades
A janela Propriedades está no painel mais certo da aplicação monitoring e gestão.

![Janela de propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Exibe propriedades para o item que selecionou na lista do Explorador de Recursos (vista para a árvore), na visualização do diagrama ou na lista do Windows de Atividade.

### <a name="activity-window-explorer"></a>Explorador de janelas de atividade
A janela **Do Explorador** da Janela de Atividade está no painel mais certo da aplicação de Monitorização e Gestão. Apresenta detalhes sobre a janela de atividade que selecionou na janela pop-up do Activity Windows ou na lista do Windows de Atividade.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Pode mudar para outra janela de atividade clicando na vista do calendário na parte superior. Também pode utilizar os botões de seta esquerda/direita na parte superior para ver janelas de atividade da semana anterior ou da semana seguinte.

Pode utilizar os botões da barra de ferramentas no painel inferior para reproduzir a janela de atividade ou refrescar os detalhes no painel.

### <a name="script"></a>Script
Pode utilizar o separador **Script** para visualizar a definição JSON da entidade selecionada data Factory (serviço ligado, conjunto de dados ou pipeline).

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Use as vistas do sistema
A aplicação de Monitorização e Gestão inclui vistas de sistema pré-construídas ( Janelas de**atividades recentes,** janelas de **atividade sintetizadas,** **janelas de atividade em curso**) que permitem visualizar janelas de atividades recentes/falhadas/em andamento para a sua fábrica de dados.

Mude para o separador Pontos de **Monitorização** à esquerda clicando nele.

![Guia de visualizações de monitorização](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, existem três pontos de vista do sistema que são apoiados. Selecione uma opção para ver janelas de atividade recentes, janelas de atividade sintetizadas ou janelas de atividade em andamento na lista Do Windows de Atividade (na parte inferior do painel médio).

Ao selecionar a opção **de janelas de atividade recente,** você vê todas as janelas de atividade recentes em ordem descendente do tempo da **última tentativa**.

Pode utilizar a visão **de janelas** de atividade falhada para ver todas as janelas de atividade falhadas na lista. Selecione uma janela de atividade falhada na lista para ver detalhes sobre o mesmo na janela **Propriedades** ou no Explorador de **Janelas**de Atividade . Também pode descarregar quaisquer registos para uma janela de atividade falhada.

## <a name="sort-and-filter-activity-windows"></a>Ordenar e filtrar janelas de atividade
Altere as definições de tempo de **início** e **de fim** na barra de comando para filtrar as janelas de atividade. Depois de alterar o tempo de início e o tempo final, clique no botão ao lado do tempo final para atualizar a lista do Windows de Atividade.

![Tempos de início e fim](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todos os momentos estão em formato UTC na aplicação de Monitorização e Gestão.
>
>

Na **lista 'Janelas de**Atividades', clique no nome de uma coluna (por exemplo: Estado).

![Menu de coluna seleções do Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Pode fazer o seguinte:

* Ordenar em ordem ascendente.
* Ordenar em ordem descendente.
* Filtrar por um ou mais valores (Pronto, À Espera e assim por diante).

Quando especifica um filtro numa coluna, vê o botão do filtro ativado para essa coluna, o que indica que os valores da coluna são valores filtrados.

![Filtrar numa coluna da lista de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pode usar a mesma janela pop-up para limpar filtros. Para limpar todos os filtros para a lista Do Windows de Atividade, clique no botão de filtro claro na barra de comando.

![Limpe todos os filtros para a lista de Janelas de Atividades](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Realizar ações de lote
### <a name="rerun-selected-activity-windows"></a>Reexecutar janelas de atividade selecionadas
Selecione uma janela de atividade, clique na seta para baixo para o primeiro botão de barra de comando e selecione **Reexecutar** / **Reexecutar com**a montante no gasoduto . Quando seleciona o **Rerun com** opção pipeline a montante, repete todas as janelas de atividade a montante também.
    ![Reexecutar uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também pode selecionar várias janelas de atividade na lista e reexecutá-las ao mesmo tempo. É melhor filtrar as janelas de atividadecom base no estado (por exemplo: Falhou)---- e depois reexecutar as janelas de atividade falhadas após corrigir o problema que faz com que as janelas de atividade falhem. Consulte a secção seguinte para obter detalhes sobre filtrar as janelas de atividade na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/retomar vários oleodutos
Pode selecionar dois ou mais oleodutos utilizando a tecla CTRL. Pode utilizar os botões da barra de comando (que são realçados no retângulo vermelho na imagem seguinte) para parar/retomá-los.

![Pausa/retoma na barra de comando](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
