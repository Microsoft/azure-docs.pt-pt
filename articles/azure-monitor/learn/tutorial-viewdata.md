---
title: Ver ou analisar dados do Azure Log Analytics recolhidos | Microsoft Docs
description: Este artigo inclui um tutorial que descreve como criar pesquisas de registos e analisar os dados armazenados no seu recurso do Log Analytics através do portal da Pesquisa de Registos.  O tutorial inclui a execução de algumas consultas simples para devolver diferentes tipos de dados e analisar os resultados.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c64c0837956c59cf7a810bb06ca63bc21c5158d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590268"
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Ver ou analisar dados recolhidos com a pesquisa de registos do Log Analytics

No Log Analytics, pode tirar partido das pesquisas de registos ao criar consultas que analisem os dados recolhidos e utilizar dashboards pré-existentes que podem ser personalizados com vistas gráficas das suas pesquisas mais importantes.  Agora que definiu a recolha de dados de operações das suas VMs do Azure e dos seus Registos de Atividades, vai aprender, neste tutorial, a:

> [!div class="checklist"]
> * Fazer uma pesquisa simples de dados de eventos e utilizar funcionalidades para modificar e filtrar os resultados 
> * Aprender a trabalhar com dados de desempenho

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md).  

A criação e edição de consultas, para além de trabalhar interativamente com os dados devolvidos, pode ser feita de duas formas.  Para consultas básicas, utilize a página Pesquisa de Registos no portal do Azure e, para consultas avançadas, pode utilizar o portal Análise Avançada. Para saber mais sobre a diferença na funcionalidade dos dois portais, veja [Portals for creating and editing log queries in Azure Log Analytics](../../azure-monitor/log-query/portals.md) (Portais para criar e editar consultas de registos no Azure Log Analytics).

Neste tutorial, vamos trabalhar com a Pesquisa de Registos no portal do Azure. 

## <a name="log-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Abrir o portal da Pesquisa de Registos 
Comece por abrir o portal do Registo de Pesquisas.   

1. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **Monitorizar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Monitorizar**.
2. No menu de navegação Monitorizar, selecione **Log Analytics** e uma área de trabalho.

## <a name="create-a-simple-search"></a>Criar uma pesquisa simples
A forma mais rápida de obter alguns dados com os quais trabalhar é utilizar uma pesquisa simples que devolve todos os registos numa tabela.  Se tiver clientes Windows ou Linux ligados à sua área de trabalho, terá dados na tabela Event (Windows) ou Syslog (Linux).

Escreva uma das consultas seguintes na caixa de pesquisa e clique no botão “search” (“pesquisar”).  

```
Event
```
```
Syslog
```

São devolvidos dados na vista de lista predefinida e pode ver o número total de registos que foram devolvidos.

![Consulta simples](media/tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Só são apresentadas as primeiras propriedades de cada registo.  Clique em **show more** (mostrar mais) para apresentar todas as propriedades de um determinado registo.

## <a name="filter-results-of-the-query"></a>Filtrar os resultados da consulta
No lado esquerdo do ecrã está disponível o painel de filtro, que lhe permite adicionar filtragem à consulta sem modificá-la diretamente.  São apresentadas várias propriedades do registo para esse tipo de registo e pode selecionar um ou mais valores de propriedade para reduzir os resultados da pesquisa.

Se estiver a trabalhar com **Event**, selecione a caixa de verificação junto a **Error** (Erro), em **EVENTLEVELNAME**.   Se estiver a trabalhar com **Syslog**, selecione a caixa de verificação junto a **Error**, em **SEVERITYLEVEL**.  Desta forma, a consulta é alterada para uma das seguintes, de modo a limitar os resultados a eventos de erro.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtro](media/tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Adicione propriedades ao painel do filtro ao selecionar **Add to filters** (Adicionar aos filtros) no menu de propriedades de um dos registos.

![Adicionar ao menu de filtro](media/tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Pode definir o mesmo filtro ao selecionar **Filter** (Filtro) no menu de propriedades de um registo com o valor pelo qual quer filtrar.  

Só as propriedades que têm o nome a azul têm disponível a opção **Filter** quando coloca o cursor sobre as mesmas.  Estes são campos *pesquisável* que são indexados para condições de pesquisa.  Os campos a cinzento são campos *de texto livre pesquisáveis* que só têm a opção **Show references** (Mostrar referências).  Esta opção devolve registos que tenham esse valor em qualquer propriedade.

Pode agrupar os resultados de uma propriedade individual ao selecionar a opção **Group by** (Agrupar por) no menu do registo.  Desta forma, é adicionado o operador [summarize](/azure/kusto/query/summarizeoperator) (resumir) à sua consulta, o qual apresenta os resultados num gráfico.  Pode agrupar mais de uma propriedade, mas terá de editar a consulta diretamente.  Selecione o menu do registo junto à propriedade **Computer** e selecione **Group by 'Computer'** (Agrupar por “Computer”).  

![Agrupar por computador](media/tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Trabalhar com os resultados
O portal do Registo de Pesquisas tem várias funcionalidades para trabalhar com os resultados de uma consulta.  Pode ordenar, filtrar e agrupar resultados para analisar os dados sem modificar a consulta em si.  Os resultados das consultas não são ordenados por predefinição.

Para ver os dados numa forma de tabela, que oferece opções adicionais para filtrar e ordenar, clique em **Table** (Tabela).  

![Vista de tabela](media/tutorial-viewdata/log-search-portal-table-01.png)

Clique na seta junto a um registo para ver os detalhes do mesmo.

![Ordenar os resultados](media/tutorial-viewdata/log-search-portal-table-02.png)

Para ordenar por qualquer campo, clique no cabeçalho da respetiva coluna.

![Ordenar os resultados](media/tutorial-viewdata/log-search-portal-table-03.png)

Filtre os resultados por um valor específico na coluna ao clicar no botão de filtro e indicar uma condição de filtro.

![Filtrar os resultados](media/tutorial-viewdata/log-search-portal-table-04.png)

Agrupe numa coluna ao arrastar o cabeçalho da mesma para a parte superior dos resultados.  Pode agrupar por vários campos ao arrastar múltiplas colunas para a parte superior.

![Agrupar os resultados](media/tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Trabalhar com dados de desempenho
Os dados de desempenho dos agentes Windows e Linux são armazenados na área de trabalho do Log Analytics, na tabela **Perf**.  Os registos de desempenho são semelhantes a qualquer outro registo e vamos escrever uma consulta simples que devolve todos os registos de desempenho, tal como fazemos com os eventos.

```
Perf
```

![Dados de desempenho](media/tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

No entanto, devolver milhões de registos para todos os objetos e contadores de desempenho não é muito útil.  Pode utilizar os mesmos métodos que utilizou acima para filtrar os dados ou escrever a seguinte consulta diretamente na caixa de pesquisa de registos.  Esta ação devolve apenas registos de utilização do processador dos computadores Windows e Linux.

```
Perf | where ObjectName == "Processor"  | where CounterName == "% Processor Time"
```

![Utilização do processador](media/tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Isto limita os dados a um contador específico, mas ainda não os põe numa forma que seja particularmente útil.  Pode apresentar os dados num gráfico de linhas, mas primeiro tem de agrupá-los por Computer e TimeGenerated.  Para agrupar em vários campos, tem de modificar a consulta diretamente, por isso, modifique-a para o seguinte.  Esta alteração utiliza a função [avg](/azure/kusto/query/avg-aggfunction) na propriedade **CounterValue** para calcular o valor médio ao longo de cada hora.

```
Perf  
| where ObjectName == "Processor"  | where CounterName == "% Processor Time"
| summarize avg(CounterValue) by Computer, TimeGenerated
```

![Gráfico de dados de desempenho](media/tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Agora que os dados estão devidamente agrupados, pode apresentá-los num gráfico visual ao adicionar o operador [render](/azure/kusto/query/renderoperator).  

```
Perf  
| where ObjectName == "Processor" | where CounterName == "% Processor Time" 
| summarize avg(CounterValue) by Computer, TimeGenerated 
| render timechart
```

![Gráfico de linhas](media/tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a criar pesquisas de registos básicas para analisar dados de eventos e desempenho.  Avance para o próximo tutorial para saber como visualizar os dados através da criação de um dashboard.

> [!div class="nextstepaction"]
> [Create and share Log Analytics dashboards](tutorial-logs-dashboards.md) (Criar e partilhar dashboards do Log Analytics)
