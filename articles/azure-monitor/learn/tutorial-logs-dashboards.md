---
title: Criar e partilhar dashboards de dados do Log Analytics do Azure | Microsoft Docs
description: Este tutorial ajuda-o a compreender de que forma os dashboards do Log Analytics podem visualizar todas as pesquisas de registo guardadas, dando-lhe uma visão única do seu ambiente.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5ed0cfba9abaed1f1fdbacc8fcf28918403b82f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590087"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e partilhar dashboards dos dados do Log Analytics

Os dashboards do Log Analytics podem visualizar todas as suas pesquisas de registo guardadas, dando-lhe a capacidade de encontrar, correlacionar e partilhar dados operacionais de TI na organização.  Este tutorial abrange a criação de uma pesquisa de registo que será utilizada para suportar um dashboard partilhado que será acedido pela sua equipa de suporte de operações de TI.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard partilhado no portal do Azure
> * Visualizar uma pesquisa de registo de desempenho 
> * Adicionar uma pesquisa de registo a um dashboard partilhado 
> * Personalizar um mosaico num dashboard partilhado

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um dashboard partilhado

A primeira coisa que vê depois de iniciar sessão no portal do Microsoft Azure é um [dashboard](../../azure-portal/azure-portal-dashboards.md).<br> ![Dashboard do portal do Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Aqui, pode reunir os dados operacionais mais importantes para as TI em todos os recursos do Azure, incluindo telemetria do Log Analytics do Azure.  Antes de avançarmos para visualizar uma pesquisa de registos, vamos primeiro criar um dashboard e partilhá-lo.  Isto permite-nos tirá-lo do caminho, antes de realizarmos a nossa pesquisa de registos de desempenho de exemplo, que será composta como um gráfico de linhas e adicioná-la ao dashboard.  

Para criar um dashboard, selecione o botão **Novo dashboard** junto ao nome do dashboard atual.<br> ![Criar novo dashboard no portal do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Esta ação cria um dashboard novo, vazio e privado, e coloca-o no modo de personalização onde pode nomear o seu dashboard e adicionar ou reorganizar os mosaicos. Edite o nome do dashboard e especifique o *Dashboard de Exemplo* para este tutorial e, em seguida, selecione **Personalização concluída**.<br><br> ![Guardar dashboard do Azure personalizado](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador que o pode ver. Para o tornar visível a outros utilizadores, utilize o botão **Partilhar** que se encontra junto aos outros comandos do dashboard.<br> ![Partilhar um novo dashboard no portal do Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

É-lhe pedido para escolher uma subscrição e um grupo de recursos para o seu dashboard ser publicado. Para sua comodidade, a experiência de publicação do portal encaminha-o para um padrão onde coloca dashboards num grupo de recursos designado **dashboards**.  Verifique a subscrição selecionada e, em seguida, clique em **Publicar**.  O acesso às informações apresentadas no dashboard é controlado com o [Controlo de Acesso Baseado no Recurso do Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-search"></a>Visualizar uma pesquisa de registos

Pode criar consultas básicas numa única linha a partir do portal da Pesquisa de Registos no portal do Azure. O portal da Pesquisa de Registos pode ser utilizado sem iniciar um portal externo e pode utilizá-lo para realizar uma variedade de funções com pesquisas de registos, incluindo a criação de regras de alerta, a criação de grupos de computadores e a exportação dos resultados da consulta. 

O [portal do Log Analytics](../../azure-monitor/log-query/get-started-portal.md) é um portal dedicado que oferece funcionalidades avançadas que não estão disponíveis no portal da Pesquisa de Registos. As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, executar código seletivamente, contexto confidencial do Intellisense e Análise Inteligente. No portal da Análise Avançada, irá criar uma vista de desempenho no formulário gráfico, guardá-lo para uma pesquisa futura e afixá-lo ao dashboard partilhado que criou anteriormente.   

Inicie o portal da Análise Avançada a partir de uma ligação no portal da Pesquisa de Registos.<br> ![Iniciar o portal da Análise Avançada](media/tutorial-logs-dashboards/log-analytics-advancedportal-01.png)

No portal da Análise, introduza a seguinte consulta para devolver apenas registos de utilização do processador para computadores Windows e Linux, agrupados por Computador e TimeGenerated, e apresentado num gráfico visual:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Guarde a consulta ao selecionar o botão **Guardar consulta** a partir do canto superior direito.<br> ![Guardar a consulta a partir do portal da Análise Avançada](media/tutorial-logs-dashboards/log-analytics-advancedportal-02.png)<br><br> No painel de controlo **Guardar Consulta**, indique um nome como *VMs do Azure - Utilização do Processador* e, em seguida, clique em **Guardar**.  Desta forma, pode criar uma biblioteca de consultas comuns para procurar ou modificar a mesma, sem ter de voltar a escrevê-la completamente.  Por fim, afixe isto ao dashboard partilhado criado anteriormente ao selecionar o botão **Afixar gráfico ao dashboard do Azure** a partir do canto direito da página.  

Agora que temos uma consulta afixada ao dashboard, irá reparar que tem um título genérico e um comentário abaixo do mesmo.<br> ![Exemplo de dashboard do Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Devemos alterar o nome para algo significativo que possa ser facilmente compreendido pelos utilizadores que o veem.  Clique com o botão direito do rato no mosaico e selecione **Editar título**.  Quando tiver terminado de personalizar o título e o subtítulo do mosaico, clique em **Atualizar**.  Será apresentada uma faixa a pedir-lhe para publicar ou eliminar as alterações.  Clique em **Publicar alterações** e, em seguida, feche o painel de controlo **Editar Mosaico**.  

![Configuração concluída do dashboard de exemplo](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um dashboard no portal do Azure e a adicionar uma pesquisa de registos ao mesmo.  Avance para o próximo tutorial para conhecer as diferentes respostas que pode implementar com base nos resultados da pesquisa de registos.  

> [!div class="nextstepaction"]
> [Responder a eventos com Alertas do Log Analytics](tutorial-response.md)
