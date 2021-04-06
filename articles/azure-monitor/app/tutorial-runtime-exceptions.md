---
title: Diagnosticar exceções de runtime com o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar exceções de runtime na sua aplicação com o Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 98ccaef716ae2390dcbcfbc7c4a1916359115f93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628101"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Localizar e diagnosticar exceções de runtime com o Azure Application Insights

O Azure Application Insights recolhe telemetria da sua aplicação para ajudar a identificar e diagnosticar exceções de runtime.  Este tutorial guia-o ao longo deste processo com a sua aplicação.  Saiba como:

> [!div class="checklist"]
> * Modificar o seu projeto para ativar o controlo de exceções
> * Identificar exceções de diferentes componentes da aplicação
> * Ver detalhes de uma exceção
> * Transferir um instantâneo da exceção para o Visual Studio para depuração
> * Analisar os detalhes de pedidos falhados com a linguagem de consulta
> * Criar um novo item de trabalho para corrigir o código com erros


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instalar [o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Transfira e instale o [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Ative o [Visual Studio Snapshot Debugger](../app/snapshot-debugger.md).
- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../app/asp-net.md). 
- O tutorial controla a identificação de uma exceção na sua aplicação, por isso, modifique o código no seu ambiente de desenvolvimento ou de teste para gerar uma exceção. 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com) .


## <a name="analyze-failures"></a>Analisar falhas
O Application Insights recolhe as falhas ocorridas na sua aplicação e permite-lhe ver a frequência das mesmas em operações diferentes para o ajudar a centrar os seus esforços naquelas cujo impacto é mais significativo.  Pode, em seguida, desagregar os detalhes destas falhas para identificar a raiz do problema.   

1. Selecione **Application Insights** e, em seguida, a sua subscrição.  
2. Para abrir o painel **Falhas**, selecione **Falhas** no menu **Investigar** ou clique no gráfico **Pedidos falhados**.

    ![Pedidos com falhas](media/tutorial-runtime-exceptions/failed-requests.png)

3. O painel **Pedidos falhados** mostra a contagem de pedidos falhados e o número de utilizadores afetados para cada operação da aplicação.  A ordenação destas informações por utilizador permite-lhe identificar as falhas com maior impacto sobre os utilizadores.  Neste exemplo, **GET Employees/Create** e **GET Customers/Details** são candidatos prováveis a serem investigados devido ao número elevado de falhas e utilizadores afetados que apresentam.  Se selecionar uma determinada operação serão mostradas mais informações sobre a mesma no painel à direita.

    ![Painel de pedidos falhados](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Reduza o intervalo de tempo para ampliar o período em que a taxa de falhas mostra um pico.

    ![Janela de pedidos falhados](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. Veja os exemplos relacionados ao clicar no botão com o número de resultados filtrados. Os exemplos de "sugeridos" têm telemetria relacionada de todos os componentes, mesmo que a amostragem tenha estado em vigor em qualquer um deles. Clique no resultado da pesquisa para ver os detalhes da falha.

    ![Amostras dos pedidos com falhas](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. Os detalhes dos pedidos com falha mostram o gráfico de Gantt que mostra que ocorreram duas falhas de dependência nesta transação, o que também é atribuído a mais de 50% da duração total da transação. Esta experiência apresenta toda a telemetria, através de componentes de uma aplicação distribuída que estão relacionadas com este ID de operação. [Saiba mais sobre a nova experiência.](../app/transaction-diagnostics.md) Pode selecionar qualquer um dos itens para ver os respetivos detalhes no lado direito. 

    ![Detalhes dos pedidos com falhas](media/tutorial-runtime-exceptions/failed-request-details.png)

7. Os detalhes das operações também mostram um FormatException que parece estar na origem da falha.  Pode ver que o problema deve-se a um código postal inválido. É possível abrir o instantâneo de depuração para ver informações de nível de depuração de código no Visual Studio.

    ![Detalhes da exceção](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Identificar o código com falhas
O Snapshot Debugger recolhe instantâneos das exceções mais frequentes na sua aplicação para o ajudar a diagnosticar a raiz do problema na produção.  Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Em seguida, tem a opção de depurar o código fonte, descarregando o instantâneo e abrindo-o no Visual Studio 2019 Enterprise.

1. Nas propriedades da exceção, clique em **Abrir instantâneo de depuração**.
2. O painel **Depurar Instantâneo** é aberto com a pilha de chamadas relativas ao pedido.  Clique num dos métodos disponíveis para ver os valores de todas as variáveis locais no momento do pedido.  Tomando como ponto de partida o método principal neste exemplo, é possível ver variáveis locais que não têm qualquer valor.

    ![Depurar o instantâneo](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. A primeira chamada com valores válidos é **ValidZipCode** e podemos ver que foi fornecido um código postal com letras que não é possível converter num número inteiro.  Aparentemente, este é o erro no código que precisa de ser corrigido.

    ![Screenshot que mostra um erro no código que precisa de ser corrigido.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Em seguida, tem a opção de transferir esta imagem para o Visual Studio, onde podemos localizar o código real que precisa de ser corrigido. Para tal, clique em **Baixar O Snapshot**.
5. O instantâneo é carregado para o Visual Studio.
6. Agora pode executar uma sessão de depurar na Visual Studio Enterprise que rapidamente identifica a linha de código que causou a exceção.

    ![Exceção no código](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Utilizar dados de análise
Todos os dados recolhidos pelo Application Insights são armazenados no Azure Log Analytics, o qual fornece uma linguagem de consulta avançada que lhe permite analisar os dados de diversas formas.  Podemos utilizar estes dados para analisar os pedidos que geraram a exceção que estamos a pesquisar. 

1. Clique nas informações de CodeLens por cima do código para ver a telemetria fornecida pelo Application Insights.

    ![Código](media/tutorial-runtime-exceptions/codelens.png)

1. Clique em **Analisar o impacto** para abrir o Application Insights Analytics.  Este é povoado com várias consultas que fornecem detalhes sobre os pedidos falhados, tais como os utilizadores, os browsers e as regiões afetados.<br><br>![A screenshot mostra a janela Application Insights, que inclui várias consultas.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Adicionar item de trabalho
Se ligar o Application Insights a um sistema de controlo, como o Azure DevOps ou o GitHub, pode criar um item de trabalho diretamente a partir do Application Insights.

1. Volte ao painel **Propriedades da Exceção** no Application Insights.
2. Clique em **Novo Item de Trabalho**.
3. O painel **Novo Item de Trabalho** é aberto com os detalhes sobre a exceção já povoados.  Pode adicionar as informações adicionais que quiser antes de o guardar.

    ![Novo Item de Trabalho](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a identificar exceções de runtime, avance para o próximo tutorial para saber como identificar e diagnosticar problemas de desempenho.

> [!div class="nextstepaction"]
> [Identificar problemas de desempenho](./tutorial-performance.md)

