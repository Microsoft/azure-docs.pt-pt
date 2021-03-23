---
title: Monitorizar e gerir os gasodutos de dados - Azure
description: Saiba como utilizar a app de Monitorização e Gestão para monitorizar e gerir fábricas de dados e oleodutos Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3a9eb73aab381f90ae51300bff1250348916c2ab
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779601"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorize e gere os oleodutos da Azure Data Factory utilizando a app de monitorização e gestão
> [!div class="op_single_selector"]
> * [Usando o portal Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando a app de monitorização e gestão](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [o monitor e gere os oleodutos data factory em](../monitor-visually.md).

Este artigo descreve como utilizar a app de Monitorização e Gestão para monitorizar, gerir e depurar os seus oleodutos data factory. Pode começar a utilizar a aplicação assistindo ao seguinte vídeo:

> [!NOTE]
> A interface do utilizador mostrada no vídeo pode não corresponder exatamente ao que vê no portal. É um pouco mais velho, mas os conceitos permanecem os mesmos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Lançar a app de Monitorização e Gestão
Para lançar a aplicação Monitor and Management, clique no **monitor & Gerir** o azulejo na lâmina da Fábrica de **Dados** para a sua fábrica de dados.

![Azulejo de monitorização na página inicial da Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Deve ver a aplicação de Monitorização e Gestão aberta numa janela separada.  

![Aplicação de Monitorização e Gestão](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se vir que o navegador está preso em "Authorizing...", limpe os **cookies de terceiros do Bloco e** a caixa de verificação de dados do site -- ou mantenha-o selecionado, crie uma exceção para **login.microsoftonline.com**, e tente abrir novamente a app.


Na lista do Activity Windows no painel central, vê-se uma janela de atividade para cada execução de uma atividade. Por exemplo, se tiver a atividade programada para funcionar de hora a hora durante cinco horas, vê cinco janelas de atividade associadas a cinco fatias de dados. Se não vir janelas de atividade na lista na parte inferior, faça o seguinte:
 
- Atualize os filtros **de tempo** de início e de fim de **hora** na parte superior para corresponder aos tempos de início e fim do seu pipeline e, em seguida, clique no botão **Aplicar.**  
- A lista do Windows de atividade não é automaticamente atualizada. Clique no botão **Refresh** na barra de ferramentas na lista **'Windows' de atividade.**  

Se não tiver uma aplicação de Data Factory para testar estes passos, faça o tutorial: [copie os dados do Blob Storage para a BASE de Dados SQL utilizando a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Compreender a app de Monitorização e Gestão
Existem três separadores à esquerda: **Explorador de Recursos,** **Vistas de Monitorização** e **Alertas.** O primeiro separador **(Explorador de Recursos)** é selecionado por predefinição.

### <a name="resource-explorer"></a>Explorador de Recursos
Vê o seguinte:

* A vista da **árvore** exploradora de recursos no painel esquerdo.
* A **vista do diagrama** na parte superior no painel central.
* A lista **de Activity Windows** na parte inferior do painel central.
* As **propriedades,** **explorador de janelas** de atividade e separadores **script** no painel direito.

No Resource Explorer, vê todos os recursos (oleodutos, conjuntos de dados, serviços ligados) na fábrica de dados numa vista de árvores. Quando seleciona um objeto no Explorador de Recursos:

* A entidade associada da Data Factory está em destaque na Vista diagrama.
* [As janelas de atividade associadas](data-factory-scheduling-and-execution.md) estão em destaque na lista do Activity Windows na parte inferior.  
* As propriedades do objeto selecionado são mostradas na janela Propriedades no painel direito.
* A definição JSON do objeto selecionado é mostrada, se aplicável. Por exemplo: um serviço ligado, um conjunto de dados ou um oleoduto.

![Explorador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo [de Agendamento e Execução](data-factory-scheduling-and-execution.md) para obter informações conceptuais detalhadas sobre janelas de atividades.

### <a name="diagram-view"></a>Vista de Diagrama
A Vista Diagrama de uma fábrica de dados fornece um único painel de vidro para monitorizar e gerir uma fábrica de dados e seus ativos. Quando selecionar uma entidade da Data Factory (conjunto de dados/pipeline) na Vista diagrama:

* A entidade de fábrica de dados é selecionada na vista da árvore.
* As janelas de atividade associadas estão em destaque na lista do Activity Windows.
* As propriedades do objeto selecionado são mostradas na janela Propriedades.

Quando o gasoduto está ativado (não em estado de pausa), é mostrado com uma linha verde:

![Gasoduto em execução](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Pode fazer uma pausa, retomar ou terminar um oleoduto selecionando-o na vista do diagrama e utilizando os botões da barra de comando.

![Pausa/currículo na barra de comando](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existem três botões de barra de comando para o gasoduto na Vista diagrama. Pode utilizar o segundo botão para fazer uma pausa na tubagem. A pausa não encerra as atividades atualmente em execução e permite-lhes prosseguir até à conclusão. O terceiro botão interrompe o gasoduto e termina as suas atividades de execução existentes. O primeiro botão retoma o gasoduto. Quando o seu oleoduto é pausado, a cor do gasoduto muda. Por exemplo, um gasoduto pausado parece na seguinte imagem: 

![Gasoduto parada](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pode selecionar duas ou mais condutas utilizando a tecla Ctrl. Pode utilizar os botões da barra de comando para interromper/retomar vários oleodutos de cada vez.

Também pode clicar com o botão direito de um pipeline e selecionar opções para suspender, retomar ou terminar um oleoduto. 

![Menu de contexto para pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique na opção **de gasoduto Open** para ver todas as atividades no oleoduto. 

![Menu Abrir pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na vista do gasoduto aberto, vê-se todas as atividades no oleoduto. Neste exemplo, há apenas uma atividade: Copiar Atividade. 

![Oleoduto aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para voltar à vista anterior, clique no nome da fábrica de dados no menu de migalhas de pão no topo.

Na vista do gasoduto, quando seleciona um conjunto de dados de saída ou quando move o rato sobre o conjunto de dados de saída, vê a janela pop-up do Activity Windows para esse conjunto de dados.

![Janela pop-up do Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Pode clicar numa janela de atividade para ver detalhes na janela **Propriedades** no painel direito.

![Propriedades da janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel direito, mude para o **separador Activity Window Explorer** para ver mais detalhes.

![Screenshot que mostra como aceder ao separador Activity Window Explorer.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Também vê **variáveis resolvidas** para cada tentativa de execução de uma atividade na secção **Tentativas.**

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mude para o **separador Script** para ver a definição de script JSON para o objeto selecionado.   

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Você pode ver janelas de atividade em três lugares:

* O pop-up do Activity Windows na vista do diagrama (painel do meio).
* O Explorador da Janela de Atividade no painel direito.
* A lista de Janelas de Atividade no painel inferior.

No pop-up do Activity Windows e no Activity Window Explorer, pode deslocar-se para a semana anterior e para a semana seguinte utilizando as setas esquerda e direita.

![Setas do Explorador da janela de atividade esquerda/direita](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da vista do diagrama, vê estes botões: Zoom In, Zoom out, Zoom to Fit, Zoom 100%, Lock layout. O botão **de disposição Lock** impede-o de mover acidentalmente mesas e oleodutos na Vista diagrama. É por defeito. Pode desligá-lo e mover entidades no diagrama. Quando o desligar, pode utilizar o último botão para posicionar automaticamente as mesas e os oleodutos. Também pode fazer zoom dentro ou fora usando a roda do rato.

![Diagrama Ver comandos de zoom](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Janelas de Atividades
A lista de Janelas de Atividade na parte inferior do painel central exibe todas as janelas de atividade para o conjunto de dados que selecionou no Explorador de Recursos ou na Vista do Diagrama. Por predefinição, a lista encontra-se em ordem descendente, o que significa que vê a última janela de atividade no topo.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista não se atualiza automaticamente, por isso utilize o botão de atualização na barra de ferramentas para o refrescar manualmente.  

As janelas de atividade podem estar num dos seguintes estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subtátuo</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">Em espera</td><td>Horário</td><td>Não chegou a hora da janela de atividade funcionar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>Lírita de concurrencyLimit</td> <td>Todas as situações de atividade estão ocupadas a gerir outras janelas de atividade.</td>
</tr>
<tr>
<td>AtividadesResume</td><td>A atividade é interrompida e não pode executar as janelas de atividade até que seja retomada.</td>
</tr>
<tr>
<td>Tentar novamente</td><td>A execução da atividade está a ser novamente julgada.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não começou.</td>
</tr>
<tr>
<td>ValidaçãoRetry</td><td>A validação está à espera de ser novamente julgada.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validação</td><td>A validação está em curso.</td>
</tr>
<td>-</td>
<td>A janela de atividade está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Com falhas</td><td>TimedOut</td><td>A execução da atividade demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividade foi cancelada pela ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>A janela de atividade não foi gerada ou validada.</td>
</tr>
<td>Pronto</td><td>-</td><td>A janela de atividade está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td>-</td><td>A janela de atividade não foi processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma janela de atividade costumava existir com um estado diferente, mas foi reposta.</td>
</tr>
</table>


Quando clica numa janela de atividade da lista, vê detalhes sobre o mesmo no **Activity Windows Explorer** ou na janela **Propriedades** à direita.

![Screenshot que mostra como ver detalhes sobre uma janela de atividade.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar janelas de atividade
Os detalhes não são automaticamente atualizados, por isso utilize o botão de atualização (o segundo botão) na barra de comando para atualizar manualmente a lista de janelas de atividade.  

### <a name="properties-window"></a>Janela de propriedades
A janela Properties está no painel mais à direita da aplicação de Monitorização e Gestão.

![Janela de propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Apresenta propriedades para o item que selecionou na lista de Exploradores de Recursos (vista para árvores), Diagrama ou Windows de atividade.

### <a name="activity-window-explorer"></a>Explorador de janelas de atividade
A janela **do Explorador de janelas** de atividade está no painel mais à direita da aplicação de monitorização e gestão. Apresenta detalhes sobre a janela de atividade que selecionou na janela pop-up do Activity Windows ou na lista do Activity Windows.

![Explorador de janelas de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Pode mudar para outra janela de atividade clicando na vista do calendário na parte superior. Também pode utilizar os botões de seta esquerda/direita na parte superior para ver janelas de atividade da semana anterior ou da semana seguinte.

Pode utilizar os botões da barra de ferramentas no painel inferior para voltar a revesser a janela de atividade ou refrescar os detalhes no painel.

### <a name="script"></a>Script
Pode utilizar o **separador Script** para visualizar a definição JSON da entidade selecionada da Data Factory (serviço ligado, conjunto de dados ou pipeline).

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Use as vistas do sistema
A aplicação monitoring and Management inclui vistas de sistema pré-construídas **(janelas de atividade recentes,** janelas de **atividades falhadas,** **janelas de atividade in-Progress**) que permitem visualizar janelas de atividades recentes/falhadas/em andamento para a sua fábrica de dados.

Mude para o **separador Visualizações de Monitorização** à esquerda clicando nele.

![Separador de vistas de monitorização](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, existem três pontos de vista do sistema que são suportados. Selecione uma opção para ver janelas de atividade recentes, janelas de atividade falhadas ou janelas de atividade em curso na lista do Activity Windows (na parte inferior do painel central).

Ao selecionar a opção **janelas de atividades recentes,** vê todas as janelas de atividades recentes na ordem descendente da **última tentativa.**

Pode utilizar a vista de **janelas de atividade falhada** para ver todas as janelas de atividade falhadas na lista. Selecione uma janela de atividade falhada na lista para ver detalhes sobre o mesmo na janela **Propriedades** ou no **Explorador de Janelas** de Atividade . Também pode descarregar quaisquer registos para uma janela de atividade falhada.

## <a name="sort-and-filter-activity-windows"></a>Ordenar e filtrar janelas de atividade
Altere as definições **de hora de início** e de fim de **tempo** na barra de comando para filtrar as janelas de atividade. Depois de alterar a hora de início e o tempo de fim, clique no botão ao lado da hora final para atualizar a lista de Windows de atividade.

![Horários de início e fim](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todos os tempos estão em formato UTC na app de Monitorização e Gestão.
>
>

Na **lista 'Windows' de atividade,** clique no nome de uma coluna (por exemplo: Estado).

![Menu de coluna de lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Pode realizar as seguintes acções:

* Ordenar em ordem ascendente.
* Ordenar em ordem descendente.
* Filtrar por um ou mais valores (Pronto, Espera, e assim por diante).

Quando especificar um filtro numa coluna, vê o botão do filtro ativado para essa coluna, o que indica que os valores na coluna são valores filtrados.

![Filtrar numa coluna da lista de Janelas de Atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pode utilizar a mesma janela pop-up para limpar filtros. Para limpar todos os filtros da lista 'Windows' de atividade, clique no botão de filtro claro na barra de comando.

![Limpe todos os filtros para a lista de Windows de atividade](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Realizar ações de lote
### <a name="rerun-selected-activity-windows"></a>Rerun janelas de atividade selecionadas
Selecione uma janela de atividade, clique na seta para baixo para o primeiro botão de barra de comando e selecione   /  **Rerun rerun com a montante no pipeline**. Quando seleciona a **Rerun com a montante na** opção pipeline, também volta a repetir todas as janelas de atividade a montante.
    ![Repercuta uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também pode selecionar várias janelas de atividade na lista e repeti-las ao mesmo tempo. É melhor filtrar as janelas de atividade com base no estado (por exemplo: **Falhados**)- e, em seguida, voltar a repetir as janelas de atividade falhadas depois de corrigir o problema que faz com que as janelas de atividade falhem. Consulte a seguinte secção para obter mais informações sobre as janelas de atividade de filtragem na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/retomar vários oleodutos
Pode utilizar a chave Ctrl com uma várias ou mais condutas. Pode utilizar os botões da barra de comando (que são realçados no retângulo vermelho na imagem a seguir) para fazer uma pausa/retomá-los.

![Pausa/currículo na barra de comando](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
