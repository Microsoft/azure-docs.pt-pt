---
title: Criar e partilhar dashboards de dados do Log Analytics do Azure | Microsoft Docs
description: Este tutorial ajuda-o a compreender como os dashboards do Log Analytics podem visualizar-todas as suas consultas de registo guardadas, dando-lhe uma lente única para ver o seu ambiente.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296435"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e partilhar dashboards dos dados do Log Analytics

O log Analytics dashboards podem visualizar todas as suas consultas de registo guardadas, dando-lhe a possibilidade de encontrar, correlacionar e partilhar dados operacionais de TI da organização.  Este tutorial abrange a criação de uma consulta de registo que será utilizada para dar suporte a um dashboard partilhado que será acedido pela sua equipa de suporte de operações de TI.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard partilhado no portal do Azure
> * Visualizar uma consulta de registo de desempenho 
> * Adicionar uma consulta de registo para um dashboard partilhado 
> * Personalizar um mosaico num dashboard partilhado

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um dashboard partilhado
Selecione **Dashboard** para abrir o predefinido [dashboard](../../azure-portal/azure-portal-dashboards.md). Seu dashboard terá um aspeto diferente do exemplo abaixo.

![Dashboard do portal do Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Aqui, pode reunir os dados operacionais mais importantes para as TI em todos os recursos do Azure, incluindo telemetria do Log Analytics do Azure.  Antes de avançarmos para visualizar uma consulta de registo, vamos primeiro criar um dashboard e partilhá-lo.  Pode, em seguida, abordaremos nossa consulta de registo de desempenho de exemplo, que serão compostos como um gráfico de linhas e adicioná-lo ao dashboard.  

Para criar um dashboard, selecione o botão **Novo dashboard** junto ao nome do dashboard atual.

![Criar novo dashboard no portal do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Esta ação cria um dashboard novo, vazio e privado, e coloca-o no modo de personalização onde pode nomear o seu dashboard e adicionar ou reorganizar os mosaicos. Editar o nome do dashboard e especifique *Dashboard de exemplo* para este tutorial e, em seguida, selecione **personalização concluída**.<br><br> ![Guardar dashboard do Azure personalizado](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador que o pode ver. Para o tornar visível a outros utilizadores, utilize o botão **Partilhar** que se encontra junto aos outros comandos do dashboard.

![Partilhar um dashboard novo no portal do Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

É-lhe pedido para escolher uma subscrição e um grupo de recursos para o seu dashboard ser publicado. Para sua comodidade, a experiência de publicação do portal encaminha-o para um padrão onde coloca dashboards num grupo de recursos designado **dashboards**.  Verifique a subscrição selecionada e, em seguida, clique em **Publicar**.  O acesso às informações apresentadas no dashboard é controlado com o [Controlo de Acesso Baseado no Recurso do Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualizar uma consulta de registo
[Log Analytics](../log-query/get-started-portal.md) é um portal dedicado usado para trabalhar com consultas de registo e seus resultados. As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, executar código seletivamente, contexto confidencial do Intellisense e Análise Inteligente. Neste tutorial, irá utilizar o Log Analytics para criar uma vista de desempenho no formulário gráfico, guardá-lo para uma consulta futura e afixá-la para o dashboard partilhado criado anteriormente.

Abra o Log Analytics, selecionando **registos** no menu do Azure Monitor. Ele começa com uma nova consulta em branco.

![Página de boas-vindas](media/tutorial-logs-dashboards/homepage.png)

Introduza a seguinte consulta para devolver registos de utilização para computadores Windows e Linux, agrupados por computador e TimeGenerated e apresentado num gráfico visual de processador. Clique em **executar** para executar a consulta e ver o gráfico resultante.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Guardar a consulta ao selecionar o **guardar** botão na parte superior da página.

![Guardar consulta](media/tutorial-logs-dashboards/save-query.png)

Na **Guardar consulta** painel de controlo, forneça um nome, tal como *VMs do Azure - utilização do processador* e uma categoria, como *Dashboards* e, em seguida, clique em **guardar** .  Desta forma, pode criar uma biblioteca de consultas comuns que pode utilizar e modificar.  Por fim, afixe isto ao dashboard partilhado criado anteriormente ao selecionar o **Pin** botão a partir do canto superior direito da página e, em seguida, selecionar o nome do dashboard.

Agora que temos uma consulta afixada ao dashboard, irá reparar que tem um título genérico e um comentário abaixo do mesmo.

![Exemplo de dashboard do Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Devemos alterar o nome para algo significativo que possa ser facilmente compreendido pelos utilizadores que o veem.  Clique no botão Editar para personalizar o título e subtítulo do mosaico e, em seguida, clique em **atualização**.  Será apresentada uma faixa a pedir-lhe para publicar ou eliminar as alterações.  Clique em **guarde uma cópia**.  

![Configuração concluída do dashboard de exemplo](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a criar um dashboard no portal do Azure e adicionar-lhe uma consulta de registo.  Avance para o próximo tutorial para conhecer as diferentes respostas que pode implementar com base nos resultados de consulta do log.  

> [!div class="nextstepaction"]
> [Responder a eventos com Alertas do Log Analytics](tutorial-response.md)
