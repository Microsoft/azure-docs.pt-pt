---
title: Diagnosticar problemas de desempenho com o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar problemas de desempenho na sua aplicação com o Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: df7cfff7d5bf1b89f88105f79c072d1d7e731b31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005491"
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

- Instalar [o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../app/asp-net.md).
- [Ativar o gerador de perfis do Application Insights](../app/profiler.md#installation) para a sua aplicação.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com) .

## <a name="identify-slow-server-operations"></a>Identificar as operações de servidor lentas
O Application Insights recolhe detalhes de desempenho das várias operações da sua aplicação. Ao identificar as operações com a duração mais longa, pode diagnosticar potenciais problemas ou direcionar melhor o desenvolvimento contínuo para melhorar o desempenho global da aplicação.

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  
1. Para abrir o painel **Desempenho**, selecione **Desempenho** no menu **Investigar** ou clique no gráfico **Tempo de Resposta do Servidor**.

    ![Desempenho](media/tutorial-performance/1-overview.png)

2. O painel **Desempenho** mostra a contagem e a duração média de cada operação da aplicação.  Pode utilizar estas informações para identificar as operações que têm maior impacto nos utilizadores. Neste exemplo, **GET Customers/Details** e **GET Home/Index** são candidatos prováveis à investigação, devido à relativamente elevada duração e ao número de chamadas.  Outras operações podem ter uma duração superior, mas foram raramente chamadas, pelo que o efeito da respetiva melhoria seria mínimo.  

    ![Painel de servidor de desempenho](media/tutorial-performance/2-server-operations.png)

3. O gráfico mostra a duração média das operações selecionadas ao longo do tempo. Pode mudar para o percentil 95 para localizar os problemas de desempenho. Adicione as operações nas quais tem interesse, afixando-as ao gráfico.  Isto mostra que existem alguns picos que vale a investigar.  Isole isto ainda mais ao reduzir a janela de tempo do gráfico.

    ![Afixar operações](media/tutorial-performance/3-server-operations-95th.png)

4.  O painel de desempenho à direita mostra a distribuição das durações para pedidos diferentes para a operação selecionada.  Reduza a janela para iniciar por volta do percentil 95. O cartão de informações de "3 dependências principais" pode informar que as dependências externas provavelmente estão a contribuir para as transações lentas rapidamente.  Clique no botão com o número de amostras para ver uma lista dos exemplos. Em seguida, pode selecionar qualquer amostra para ver os detalhes da transação.

5.  Pode ver rapidamente que a chamada para Tabelas do Azure Fabrikamaccount é a que mais contribui para a duração total da transação. Também pode ver uma exceção causou a falha. Pode clicar em qualquer item na lista para ver os respetivos detalhes no lado direito. [Saiba mais sobre a experiência de transação de diagnósticos](../app/transaction-diagnostics.md)

    ![Operação detalhes de ponta a ponta](media/tutorial-performance/4-end-to-end.png)
    

6.  O [**Profiler**](../app/profiler-overview.md) ajuda a ir mais longe com os diagnósticos de nível de código, mostrando o código real que correu para a operação e o tempo necessário para cada passo. Algumas operações podem não ter um rastreio, dado que o gerador de perfis é executado periodicamente.  Ao longo do tempo, mais operações devem ter rastreios.  Para iniciar o gerador de perfis para a operação, clique em **Rastreios do gerador de perfis**.
5.  O rastreio mostra os eventos individuais de cada operação, para que possa diagnosticar a causa raiz da duração da operação global.  Clique num dos exemplos superiores, que têm a duração mais longa.
6.  Clique **em Hot Path** para destacar o percurso específico dos eventos que mais contribuem para a duração total da operação.  Neste exemplo, pode ver que a chamada mais lenta é a do método *FabrikamFiberAzureStorage.GetStorageTableData*. A parte que demora mais tempo é o método *CloudTable.CreateIfNotExist*. Se esta linha de código for executada sempre que a função é chamada, serão consumidas chamadas de rede e recursos de CPU desnecessários. A melhor forma de corrigir o seu código é colocar esta linha em algum método de arranque que seja executado apenas uma vez.

    ![Detalhes do gerador de perfis](media/tutorial-performance/5-hot-path.png)

7.  A **Sugestão de Desempenho** na parte superior do ecrã apoia a avaliação de que a duração excessiva se deve ao tempo de espera.  Clique na ligação **a aguardar** para obter documentação sobre como interpretar os diferentes tipos de eventos.

    ![Sugestão de desempenho](media/tutorial-performance/6-perf-tip.png)

8.   Para mais análises, clique em **Download Trace** para descarregar o rasto. Pode ver estes dados usando [o PerfView.](https://github.com/Microsoft/perfview#perfview-overview)

## <a name="use-logs-data-for-server"></a>Utilize dados de registos para servidor
 Os registos fornecem uma linguagem de consulta rica que permite analisar todos os dados recolhidos pela Application Insights. Pode utilizá-lo para efetuar uma análise detalhada dos dados de pedido e desempenho.

1. Volte ao painel de detalhes da operação e clique ![ em 'Logs](media/tutorial-performance/app-viewinlogs-icon.png)**icon' Ver em Registos (Analytics)**

2. Os registos abrem-se com uma consulta para cada uma das vistas do painel.  Pode executar estas consultas como estão ou modificá-las de acordo com os seus requisitos.  A primeira consulta mostra a duração desta operação ao longo do tempo.

    ![consulta de troncos](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identificar as operações do cliente lentas
Além de identificar os processos de servidor a otimizar, o Application Insights pode analisar a perspetiva dos browsers cliente.  Isto pode ajudar a identificar possíveis melhorias nos componentes de cliente e até mesmo a identificar problemas em browsers diferentes ou em localizações diferentes.

1. Selecione **O Navegador** em **Investigação** em seguida, clique em **Desempenho do Navegador** ou selecione **Performance** sob **Investigação** e mude para o separador **Browser** clicando no botão de alternância servidor/navegador no canto superior direito para abrir o resumo do desempenho do navegador. Esta opção fornece um resumo visual de várias telemetrias da sua aplicação da perspetiva do browser.

    ![Resumo do browser](media/tutorial-performance/8-browser.png)

2. Selecione num dos nomes de funcionamento e, em seguida, clique no botão de amostras azuis no canto inferior direito e selecione uma operação. Isto irá trazer os detalhes de transação de ponta a ponta e no lado direito pode ver as **Propriedades de Visualização** de Página. Isto permite-lhe visualizar detalhes do cliente que solicita a página, incluindo o tipo de navegador e a sua localização. Estas informações podem ajudá-lo a determinar se existem problemas de desempenho relacionados com tipos de clientes específicos.

    ![Vista de página](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Utilize dados de registos para o cliente
Tal como os dados recolhidos para o desempenho do servidor, o Application Insights disponibiliza todos os dados do cliente para análise profunda utilizando Logs.

1. Volte ao resumo do navegador e clique ![ em 'Registares' ícone ](media/tutorial-performance/app-viewinlogs-icon.png) **Ver em Registos (Analytics)**

2. Os registos abrem-se com uma consulta para cada uma das vistas do painel. A primeira consulta mostra a duração das diferentes vistas de página ao longo do tempo.

    ![Consulta de registos](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a identificar as exceções do tempo de execução, avance para o próximo tutorial para saber como criar alertas em resposta a falhas.

> [!div class="nextstepaction"]
> [Alertar relativamente ao estado de funcionamento das aplicações](./tutorial-alert.md)

