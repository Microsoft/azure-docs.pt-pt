---
title: Criar e partilhar dashboards de dados do Log Analytics do Azure | Microsoft Docs
description: Este tutorial ajuda você a entender como os painéis de Log Analytics podem visualizar todas as suas consultas de log salvas, oferecendo a você uma única lente para exibir seu ambiente.
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
ms.openlocfilehash: c859fb9b8b3509e8369559a3a9a4d45cb4e34125
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414160"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e partilhar dashboards dos dados do Log Analytics

Os painéis de Log Analytics podem visualizar todas as consultas de log salvas, oferecendo a você a capacidade de localizar, correlacionar e compartilhar dados operacionais de ti na organização.  Este tutorial aborda a criação de uma consulta de log que será usada para dar suporte a um painel compartilhado que será acessado pela equipe de suporte de operações de ti.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard partilhado no portal do Azure
> * Visualizar uma consulta de log de desempenho 
> * Adicionar uma consulta de log a um painel compartilhado 
> * Personalizar um mosaico num dashboard partilhado

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligada à área de trabalho do Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um dashboard partilhado
Selecione **painel** para abrir o [painel](../../azure-portal/azure-portal-dashboards.md)padrão. Seu painel terá aparência diferente do exemplo abaixo.

![Dashboard do portal do Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Aqui, pode reunir os dados operacionais mais importantes para as TI em todos os recursos do Azure, incluindo telemetria do Log Analytics do Azure.  Antes de entrarmos em visualização de uma consulta de log, vamos primeiro criar um painel e compartilhá-lo.  Em seguida, podemos nos concentrar em nossa consulta de log de desempenho de exemplo, que será renderizada como um gráfico de linhas e a adicionará ao painel.  

Para criar um dashboard, selecione o botão **Novo dashboard** junto ao nome do dashboard atual.

![Criar novo painel no portal do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Esta ação cria um dashboard novo, vazio e privado, e coloca-o no modo de personalização onde pode nomear o seu dashboard e adicionar ou reorganizar os mosaicos. Edite o nome do painel e especifique o *painel de exemplo* para este tutorial e, em seguida, selecione **personalização concluída**.<br><br> ![Guardar dashboard do Azure personalizado](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando cria um dashboard, é privado por predefinição, o que significa que é o único utilizador que o pode ver. Para o tornar visível a outros utilizadores, utilize o botão **Partilhar** que se encontra junto aos outros comandos do dashboard.

![Compartilhar um novo painel no portal do Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

É-lhe pedido para escolher uma subscrição e um grupo de recursos para o seu dashboard ser publicado. Para sua comodidade, a experiência de publicação do portal encaminha-o para um padrão onde coloca dashboards num grupo de recursos designado **dashboards**.  Verifique a subscrição selecionada e, em seguida, clique em **Publicar**.  O acesso às informações apresentadas no dashboard é controlado com o [Controlo de Acesso Baseado no Recurso do Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualizar uma consulta de log
[Log Analytics](../log-query/get-started-portal.md) é um portal dedicado usado para trabalhar com consultas de log e seus resultados. As funcionalidades incluem a capacidade de editar uma consulta em várias linhas, executar código seletivamente, contexto confidencial do Intellisense e Análise Inteligente. Neste tutorial, você usará Log Analytics para criar um modo de exibição de desempenho em formato gráfico, salvá-lo para uma consulta futura e fixá-lo no painel compartilhado criado anteriormente.

Abra Log Analytics selecionando **logs** no menu Azure monitor. Ele começa com uma nova consulta em branco.

![Home page](media/tutorial-logs-dashboards/homepage.png)

Insira a consulta a seguir para retornar os registros de utilização do processador para computadores Windows e Linux, agrupados por computador e TimeGenerated, e exibidos em um gráfico Visual. Clique em **executar** para executar a consulta e exibir o gráfico resultante.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Salve a consulta selecionando o botão **salvar** na parte superior da página.

![Guardar consulta](media/tutorial-logs-dashboards/save-query.png)

No painel de controle **Salvar consulta** , forneça um nome como *VMs do Azure-utilização do processador* e uma categoria como *painéis* e clique em **salvar**.  Dessa forma, você pode criar uma biblioteca de consultas comuns que você pode usar e modificar.  Por fim, fixe isso no painel compartilhado criado anteriormente selecionando o botão **fixar no painel** no canto superior direito da página e, em seguida, selecionando o nome do painel.

Agora que temos uma consulta afixada ao dashboard, irá reparar que tem um título genérico e um comentário abaixo do mesmo.

![Exemplo do painel do Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Devemos alterar o nome para algo significativo que possa ser facilmente compreendido pelos utilizadores que o veem.  Clique no botão Editar para personalizar o título e o subtítulo do bloco e, em seguida, clique em **Atualizar**.  Será apresentada uma faixa a pedir-lhe para publicar ou eliminar as alterações.  Clique em **salvar uma cópia**.  

![Configuração concluída do dashboard de exemplo](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu a criar um painel no portal do Azure e a adicionar uma consulta de log a ele.  Avance para o próximo tutorial para aprender as diferentes respostas que podem ser implementadas com base nos resultados da consulta de log.  

> [!div class="nextstepaction"]
> [Responder a eventos com Alertas do Log Analytics](tutorial-response.md)
