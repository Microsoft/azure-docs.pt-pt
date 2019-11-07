---
title: Monitorar e gerenciar pipelines de dados – Azure
description: Saiba como usar o aplicativo de monitoramento e gerenciamento para monitorar e gerenciar fábricas e pipelines de dados do Azure.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666931"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorar e gerenciar pipelines de Azure Data Factory usando o aplicativo de monitoramento e gerenciamento
> [!div class="op_single_selector"]
> * [Usando portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [monitorar e gerenciar pipelines de data Factory no](../monitor-visually.md).

Este artigo descreve como usar o aplicativo de monitoramento e gerenciamento para monitorar, gerenciar e depurar seus pipelines de Data Factory. Você pode começar a usar o aplicativo assistindo ao vídeo a seguir:

> [!NOTE]
> A interface do usuário mostrada no vídeo pode não corresponder exatamente ao que você vê no Portal. Ele é ligeiramente mais antigo, mas os conceitos permanecem os mesmos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Iniciar o aplicativo de monitoramento e gerenciamento
Para iniciar o aplicativo de monitoramento e gerenciamento, clique no bloco **monitorar & gerenciar** na folha **Data Factory** para seu data Factory.

![Bloco monitoramento no home page de Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Você deve ver o aplicativo de monitoramento e gerenciamento aberto em uma janela separada.  

![Aplicação de Monitorização e Gestão](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se você vir que o navegador da Web está preso em "autorizando...", desmarque a caixa de seleção **bloquear cookies de terceiros e dados do site** , ou mantenha-a selecionada, crie uma exceção para **login.microsoftonline.com**e tente abrir o aplicativo novamente.


Na lista janelas de atividades no painel central, você vê uma janela de atividade para cada execução de uma atividade. Por exemplo, se você tiver a atividade agendada para execução por hora por cinco horas, você verá cinco janelas de atividades associadas a cinco fatias de dados. Se você não vir as janelas de atividades na lista na parte inferior, faça o seguinte:
 
- Atualize os filtros **hora de início** e hora de **término** na parte superior para corresponder às horas de início e término de seu pipeline e, em seguida, clique no botão **aplicar** .  
- A lista janelas de atividades não é atualizada automaticamente. Clique no botão **Atualizar** na barra de ferramentas na lista **janelas de atividades** .  

Se você não tiver um aplicativo Data Factory para testar essas etapas com o, faça o tutorial: [copiar dados do armazenamento de BLOBs para o banco de dados SQL usando data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Entender o aplicativo de monitoramento e gerenciamento
Há três guias à esquerda: Gerenciador de **recursos**, **exibições de monitoramento**e **alertas**. A primeira guia (**Gerenciador de recursos**) é selecionada por padrão.

### <a name="resource-explorer"></a>Gerenciador de recursos
Você verá o seguinte:

* A exibição de **árvore** do Gerenciador de recursos no painel esquerdo.
* A **exibição de diagrama** na parte superior do painel central.
* A lista **janelas de atividades** na parte inferior no painel central.
* As **Propriedades**, o **Gerenciador de janelas de atividades**e as guias de **script** no painel direito.

No Gerenciador de recursos, você vê todos os recursos (pipelines, conjuntos de itens, serviços vinculados) no data factory em um modo de exibição de árvore. Quando você seleciona um objeto no Gerenciador de recursos:

* A entidade Data Factory associada é realçada no modo de exibição de diagrama.
* As [janelas de atividades associadas](data-factory-scheduling-and-execution.md) são realçadas na lista janelas de atividades na parte inferior.  
* As propriedades do objeto selecionado são mostradas na janela Propriedades no painel direito.
* A definição JSON do objeto selecionado é mostrada, se aplicável. Por exemplo: um serviço vinculado, um conjunto de um DataSet ou um pipeline.

![Gerenciador de recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo [agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações conceituais detalhadas sobre janelas de atividades.

### <a name="diagram-view"></a>Vista de Diagrama
A exibição de diagrama de um data factory fornece um único painel para monitorar e gerenciar um data factory e seus ativos. Quando você seleciona uma entidade de Data Factory (DataSet/Pipeline) no modo de exibição de diagrama:

* A entidade data factory é selecionada no modo de exibição de árvore.
* As janelas de atividades associadas são realçadas na lista janelas de atividades.
* As propriedades do objeto selecionado são mostradas no janela Propriedades.

Quando o pipeline está habilitado (não em um estado de pausa), ele é mostrado com uma linha verde:

![Pipeline em execução](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Você pode pausar, retomar ou encerrar um pipeline selecionando-o no modo de exibição de diagrama e usando os botões na barra de comandos.

![Pausar/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Há três botões de barra de comandos para o pipeline no modo de exibição de diagrama. Você pode usar o segundo botão para pausar o pipeline. Pausar não encerra as atividades em execução no momento e permite que elas prossigam para a conclusão. O terceiro botão Pausa o pipeline e encerra suas atividades em execução existentes. O primeiro botão retoma o pipeline. Quando o pipeline é pausado, a cor do pipeline é alterada. Por exemplo, um pipeline em pausa é semelhante à imagem a seguir: 

![Pipeline pausado](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Você pode selecionar dois ou mais pipelines usando a tecla CTRL. Você pode usar os botões da barra de comandos para pausar/retomar vários pipelines de cada vez.

Você também pode clicar com o botão direito do mouse em um pipeline e selecionar opções para suspender, retomar ou encerrar um pipeline. 

![Menu de contexto para pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique na opção **abrir pipeline** para ver todas as atividades no pipeline. 

![Menu Abrir pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na exibição de pipeline aberta, você vê todas as atividades no pipeline. Neste exemplo, há apenas uma atividade: atividade de cópia. 

![Pipeline aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para voltar para o modo de exibição anterior, clique no nome do data factory no menu de navegação estrutural na parte superior.

Na exibição de pipeline, quando você seleciona um conjunto de resultados de saída ou quando move o mouse sobre o conjunto de resultados de saída, você vê a janela pop-up janelas de atividades para esse conjunto de resultados.

![Janela pop-up janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Você pode clicar em uma janela de atividade para ver os detalhes dela na janela **Propriedades** no painel direito.

![Propriedades da janela de atividades](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel direito, alterne para a guia **Gerenciador de janelas de atividades** para ver mais detalhes.

![Gerenciador de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Você também verá **variáveis resolvidas** para cada tentativa de execução de uma atividade na seção **tentativas** .

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Alterne para a guia **script** para ver a definição de script JSON para o objeto selecionado.   

![Guia script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Você pode ver as janelas de atividades em três locais:

* O pop-up janelas de atividades na exibição de diagrama (painel central).
* O Gerenciador de janelas de atividades no painel direito.
* A lista janelas de atividades no painel inferior.

No pop-up janelas de atividades e no Gerenciador de janelas de atividades, você pode rolar para a semana anterior e para a próxima semana usando as setas para a esquerda e para a direita.

![Setas à esquerda/direita do Gerenciador de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da exibição de diagrama, você verá estes botões: ampliar, reduzir, ajustar nível de zoom, zoom de 100%, bloquear layout. O botão **Bloquear layout** impede que você mova acidentalmente tabelas e pipelines na exibição de diagrama. Ele está ativado por padrão. Você pode desativá-lo e mover entidades no diagrama. Ao desativá-lo, você pode usar o último botão para posicionar automaticamente as tabelas e os pipelines. Você também pode ampliar ou reduzir usando a roda do mouse.

![Comandos de zoom da exibição de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Janelas de Atividades
A lista janelas de atividades na parte inferior do painel central exibe todas as janelas de atividades do conjunto de resultados que você selecionou no Gerenciador de recursos ou no modo de exibição de diagrama. Por padrão, a lista está em ordem decrescente, o que significa que você vê a janela de atividades mais recente na parte superior.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Essa lista não é atualizada automaticamente, portanto, use o botão atualizar na barra de ferramentas para atualizá-la manualmente.  

As janelas de atividades podem estar em um dos seguintes status:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Substatus</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>Agendatime</td><td>A hora não chegou à execução da janela de atividade.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras janelas de atividades.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está pausada e não pode executar as janelas de atividades até que ela seja retomada.</td>
</tr>
<tr>
<td>Repetir</td><td>A execução da atividade está sendo repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A validação está aguardando para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Verificar</td><td>A validação está em andamento.</td>
</tr>
<td>-</td>
<td>A janela de atividade está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Com Falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividades foi cancelada por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>Falha na validação.</td>
</tr>
<tr>
<td>-</td><td>Falha ao gerar ou validar a janela de atividade.</td>
</tr>
<td>Pronto</td><td>-</td><td>A janela de atividade está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorada</td><td>-</td><td>A janela de atividades não foi processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma janela de atividade costumava existir com um status diferente, mas foi redefinida.</td>
</tr>
</table>


Ao clicar em uma janela de atividade na lista, você verá detalhes sobre ela no **Windows Explorer de atividade** ou na janela **Propriedades** à direita.

![Gerenciador de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar janelas de atividade
Os detalhes não são atualizados automaticamente, portanto, use o botão atualizar (o segundo botão) na barra de comandos para atualizar manualmente a lista janelas de atividades.  

### <a name="properties-window"></a>janela Propriedades
O janela Propriedades está no painel mais à direita do aplicativo de monitoramento e gerenciamento.

![janela Propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Ele exibe as propriedades do item que você selecionou no Gerenciador de recursos (exibição de árvore), na exibição de diagrama ou na lista de janelas de atividades.

### <a name="activity-window-explorer"></a>Gerenciador de janelas de atividades
A janela do **Gerenciador de janelas de atividades** está no painel mais à direita do aplicativo de monitoramento e gerenciamento. Ele exibe detalhes sobre a janela de atividade que você selecionou na janela pop-up janelas de atividades ou na lista janelas de atividades.

![Gerenciador de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Você pode alternar para outra janela de atividades clicando nela no modo de exibição de calendário na parte superior. Você também pode usar os botões de seta para a esquerda/seta para a direita na parte superior para ver as janelas de atividades da semana anterior ou da próxima semana.

Você pode usar os botões da barra de ferramentas no painel inferior para executar novamente a janela de atividade ou atualizar os detalhes no painel.

### <a name="script"></a>Script
Você pode usar a guia **script** para exibir a definição de JSON da entidade data Factory selecionada (serviço vinculado, conjunto de serviços ou Pipeline).

![Guia script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Usar exibições do sistema
O aplicativo de monitoramento e gerenciamento inclui exibições de sistema predefinidas (**janelas de atividades recentes**, **janelas de atividades com falha**, **janelas de atividades em andamento**) que permitem que você exiba janelas de atividades recentes/com falha/em andamento para seus dados padrões.

Alterne para a guia **exibições de monitoramento** à esquerda clicando nela.

![Guia exibições de monitoramento](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, há três exibições do sistema com suporte. Selecione uma opção para ver as janelas de atividades recentes, as janelas de atividades com falha ou as janelas de atividades em andamento na lista janelas de atividades (na parte inferior do painel central).

Ao selecionar a opção **Windows de atividade recente** , você verá todas as janelas de atividades recentes em ordem decrescente da **hora da última tentativa**.

Você pode usar a exibição **janelas de atividades com falha** para ver todas as janelas de atividades com falha na lista. Selecione uma janela de atividades com falha na lista para ver detalhes sobre ela na janela **Propriedades** ou no **Gerenciador de janelas de atividades**. Você também pode baixar todos os logs para uma janela de atividade com falha.

## <a name="sort-and-filter-activity-windows"></a>Classificar e filtrar janelas de atividades
Altere as configurações de **hora de início** e **hora de término** na barra de comandos para filtrar janelas de atividades. Depois de alterar a hora de início e a hora de término, clique no botão ao lado da hora de término para atualizar a lista janelas de atividades.

![Horários de início e término](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todas as horas estão no formato UTC no aplicativo de monitoramento e gerenciamento.
>
>

Na **lista janelas de atividades**, clique no nome de uma coluna (por exemplo: status).

![Menu de coluna da lista de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Você pode fazer o seguinte:

* Classificar em ordem crescente.
* Classificar em ordem decrescente.
* Filtrar por um ou mais valores (pronto, aguardando e assim por diante).

Ao especificar um filtro em uma coluna, você verá o botão de filtro habilitado para essa coluna, o que indica que os valores na coluna são valores filtrados.

![Filtrar em uma coluna da lista de janelas de atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Você pode usar a mesma janela pop-up para limpar filtros. Para limpar todos os filtros da lista janelas de atividades, clique no botão Limpar filtro na barra de comandos.

![Limpar todos os filtros para a lista de janelas de atividades](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Executar ações em lote
### <a name="rerun-selected-activity-windows"></a>Executar novamente as janelas de atividades selecionadas
Selecione uma janela de atividade, clique na seta para baixo do primeiro botão da barra de comandos e selecione **executar novamente** / **executar novamente com upstream no pipeline**. Quando você seleciona a opção **executar novamente com upstream no pipeline** , ele executa todas as janelas de atividade upstream também.
    ![executar novamente uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Você também pode selecionar várias janelas de atividades na lista e executá-las ao mesmo tempo. Talvez você queira filtrar as janelas de atividades com base no status (por exemplo: **falha**) – e, em seguida, executar novamente as janelas de atividades com falha depois de corrigir o problema que faz com que as janelas de atividades falhem. Consulte a seção a seguir para obter detalhes sobre como filtrar janelas de atividades na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausar/retomar vários pipelines
Você pode selecionar multiseleção dois ou mais pipelines usando a tecla CTRL. Você pode usar os botões da barra de comandos (que são realçados no retângulo vermelho na imagem a seguir) para pausá-los/retomá-los.

![Pausar/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
