---
title: Diagnosticar problemas de desempenho com o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar problemas de desempenho na sua aplicação com o Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f906ab5db35ce8b239eceac9cdc8244f230f5a77
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596019"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Localizar e diagnosticar problemas de desempenho com o Azure Application Insights

O Azure Application Insights recolhe telemetria da sua aplicação para ajudar a analisar o funcionamento e o desempenho.  Pode utilizar estas informações para identificar problemas que poderão estar a ocorrer ou para identificar melhorias na aplicação que teriam um maior impacto nos utilizadores.  Este tutorial guia-o ao longo do processo de analisar o desempenho dos componentes de servidor da sua aplicação e a perspetiva do cliente.  Saiba como:

> [!div class="checklist"]
> * Identificar o desempenho de operações do lado do servidor
> * Analisar as operações de servidor para determinar a causa raiz de um desempenho lento
> * Identificar operações mais lentas do lado do cliente
> * Analisar os detalhes de vistas de página com a linguagem de consulta


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Ativar o gerador de perfis do Application Insights](../../azure-monitor/app/profiler.md#installation) para a sua aplicação.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identificar as operações de servidor lentas
O Application Insights recolhe detalhes de desempenho das várias operações da sua aplicação. Ao identificar as operações com a duração mais longa, pode diagnosticar potenciais problemas ou direcionar melhor o desenvolvimento contínuo para melhorar o desempenho global da aplicação.

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  
1. Para abrir o painel **Desempenho**, selecione **Desempenho** no menu **Investigar** ou clique no gráfico **Tempo de Resposta do Servidor**.

    ![Desempenho](media/tutorial-performance/performance.png)

2. O painel **Desempenho** mostra a contagem e a duração média de cada operação da aplicação.  Pode utilizar estas informações para identificar as operações que têm maior impacto nos utilizadores. Neste exemplo, **GET Customers/Details** e **GET Home/Index** são candidatos prováveis à investigação, devido à relativamente elevada duração e ao número de chamadas.  Outras operações podem ter uma duração superior, mas foram raramente chamadas, pelo que o efeito da respetiva melhoria seria mínimo.  

    ![Painel de desempenho](media/tutorial-performance/performance-blade.png)

3. O gráfico mostra a duração média das operações selecionadas ao longo do tempo. Pode mudar para o percentil 95 para localizar os problemas de desempenho. Adicione as operações nas quais tem interesse, afixando-as ao gráfico.  Isto mostra que existem alguns picos que vale a investigar.  Isole isto ainda mais ao reduzir a janela de tempo do gráfico.

    ![Afixar operações](media/tutorial-performance/pin-operations.png)

4.  O painel de desempenho à direita mostra a distribuição das durações para pedidos diferentes para a operação selecionada.  Reduza a janela para iniciar por volta do percentil 95. O cartão de informações de "3 dependências principais" pode informar que as dependências externas provavelmente estão a contribuir para as transações lentas rapidamente.  Clique no botão com o número de amostras para ver uma lista dos exemplos. Em seguida, pode selecionar qualquer amostra para ver os detalhes da transação.

    ![Distribuição da duração](media/tutorial-performance/duration-distribution.png)

5.  Pode ver rapidamente que a chamada para Tabelas do Azure Fabrikamaccount é a que mais contribui para a duração total da transação. Também pode ver uma exceção causou a falha. Pode clicar em qualquer item na lista para ver os respetivos detalhes no lado direito. [Saiba mais sobre a experiência de transação de diagnósticos](../../azure-monitor/app/transaction-diagnostics.md)

    ![Detalhes da operação](media/tutorial-performance/operation-details.png)
    

6.  O **Gerador de Perfis** ajuda-o a obter mais com os diagnósticos a nível do código, mostrando o código atual que foi executado para a operação e o tempo necessário para cada passo. Algumas operações podem não ter um rastreio, dado que o gerador de perfis é executado periodicamente.  Ao longo do tempo, mais operações devem ter rastreios.  Para iniciar o gerador de perfis para a operação, clique em **Rastreios do gerador de perfis**.
5.  O rastreio mostra os eventos individuais de cada operação, para que possa diagnosticar a causa raiz da duração da operação global.  Clique num dos exemplos superiores, que têm a duração mais longa.
6.  Clique em **Mostrar Caminho Mais Utilizado** para realçar o caminho específico dos eventos que mais contribuem para a duração total da operação.  Neste exemplo, pode ver que a chamada mais lenta é a do método *FabrikamFiberAzureStorage.GetStorageTableData*. A parte que demora mais tempo é o método *CloudTable.CreateIfNotExist*. Se esta linha de código for executada sempre que a função é chamada, serão consumidas chamadas de rede e recursos de CPU desnecessários. A melhor forma de corrigir o seu código é colocar esta linha em algum método de arranque que seja executado apenas uma vez. 

    ![Detalhes do gerador de perfis](media/tutorial-performance/profiler-details.png)

7.  A **Sugestão de Desempenho** na parte superior do ecrã apoia a avaliação de que a duração excessiva se deve ao tempo de espera.  Clique na ligação **a aguardar** para obter documentação sobre como interpretar os diferentes tipos de eventos.

    ![Sugestão de desempenho](media/tutorial-performance/performance-tip.png)

8.  Para análise adicional, pode clicar em **Download .etl trace** para transferir o rastreio para o Visual Studio.

## <a name="use-analytics-data-for-server"></a>Utilizar dados de análise para o servidor
O Application Insights Analytics fornece uma linguagem de consulta avançada que permite analisar todos os dados recolhidos pelo Application Insights.  Pode utilizá-lo para efetuar uma análise detalhada dos dados de pedido e desempenho.

1. Regresse ao painel de detalhes da operação e clique no botão Análise.

    ![Botão Análise](media/tutorial-performance/server-analytics-button.png)

2. O Application Insights Analytics abre-se com uma consulta para cada uma das vistas no painel.  Pode executar estas consultas como estão ou modificá-las de acordo com os seus requisitos.  A primeira consulta mostra a duração desta operação ao longo do tempo.

    ![Análise](media/tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identificar as operações do cliente lentas
Além de identificar os processos de servidor a otimizar, o Application Insights pode analisar a perspetiva dos browsers cliente.  Isto pode ajudar a identificar possíveis melhorias nos componentes de cliente e até mesmo a identificar problemas em browsers diferentes ou em localizações diferentes.

1. Selecione **Browser** em **Investigar** para abrir o resumo do browser.  Esta opção fornece um resumo visual de várias telemetrias da sua aplicação da perspetiva do browser.

    ![Resumo do browser](media/tutorial-performance/browser-summary.png)

2.  Desloque-se para baixo até **Quais são as minhas páginas mais lentas?**.  Mostra uma lista das páginas na sua aplicação que os clientes demoraram mais tempo a carregar.  Pode utilizar esta informação para atribuir prioridades às páginas que têm o impacto mais significativo no utilizador.
3.  Clique numa das páginas para abrir o painel **Vista de página**.  No exemplo, a página **/FabrikamProd** está a mostrar uma duração média excessiva.  O painel **Vista de página** fornece detalhes sobre esta página, incluindo uma divisão dos vários intervalos de duração.

    ![Vista de página](media/tutorial-performance/page-view.png)

4.  Clique na duração máxima para inspecionar os detalhes destes pedidos.  Em seguida, clique no pedido individual para ver os detalhes do cliente que solicitou a página, incluindo o tipo de browser e a respetiva localização.  Estas informações podem ajudá-lo a determinar se existem problemas de desempenho relacionados com tipos de clientes específicos.

    ![Detalhes do pedido](media/tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Utilizar os dados de análise para o cliente
Como acontece nos dados recolhidos do desempenho do servidor, o Application Insights disponibiliza todos os dados de cliente para uma análise detalhada através da Análise.

1. Regresse ao resumo do browser e clique no ícone de Análise.

    ![Ícone de Análise](media/tutorial-performance/client-analytics-icon.png)

2. O Application Insights Analytics abre-se com uma consulta para cada uma das vistas no painel. A primeira consulta mostra a duração das diferentes vistas de página ao longo do tempo.

    ![Análise](media/tutorial-performance/client-analytics.png)

3.  O Diagnóstico Inteligente é uma funcionalidade do Application Insights Analytics que identifica padrões únicos nos dados.  Ao clicar no ponto de Diagnóstico Inteligente no gráfico de linhas, a mesma consulta é executada sem os registos que causaram a anomalia.  Os detalhes desses registos são apresentados na secção de comentários da consulta, para que possa identificar as propriedades das vistas de página que estão a causar a duração excessiva.

    ![Diagnóstico Inteligente](media/tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a identificar as exceções do tempo de execução, avance para o próximo tutorial para saber como criar alertas em resposta a falhas.

> [!div class="nextstepaction"]
> [Alertar relativamente ao estado de funcionamento das aplicações](../../azure-monitor/learn/tutorial-alert.md)
