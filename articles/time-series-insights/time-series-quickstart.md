---
title: 'Quickstart: Azure Time Series Insights explorer - Azure Time Series Insights [ Microsoft Docs'
description: Aprenda a começar com o explorador de Insights da Série De Tempo Azure. Visualize grandes volumes de dados ioT e visite características chave do seu ambiente.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/13/2020
ms.openlocfilehash: a566c55d2ac51efc5ff2650ad682f10dae2bdc31
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81272003"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Guia de introdução: Explorar o Azure Time Series Insights

Este quickstart de insights da Série De Tempo Azure ajuda-o a começar com time series Insights num ambiente de demonstração livre. Neste arranque rápido, aprende a usar o seu navegador web para visualizar grandes volumes de dados IoT e funcionalidades chave de turismo que estão geralmente disponíveis agora.

Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica como explorar e analisar milhares de milhões de eventos IoT simultaneamente. Dá-lhe uma visão global dos seus dados para que possa validar rapidamente a sua solução IoT e evitar um tempo de inatividade dispendioso para dispositivos críticos da missão. A Azure Time Series Insights ajuda-o a descobrir tendências ocultas, a detetar anomalias e a realizar análises de causas-raiz em tempo real.

Para uma flexibilidade adicional, pode adicionar insights da Série Time Azure a uma aplicação pré-existente através das suas poderosas [APIs](./time-series-insights-update-tsq.md) REST e [sDK do cliente.](https://github.com/microsoft/tsiclient) Pode utilizar as APIs para armazenar, consultar e consumir dados da série de tempo numa aplicação cliente à sua escolha. Também pode utilizar o SDK do cliente para adicionar componentes UI à sua aplicação existente.

Este quickstart de explorador da Time Series Insights oferece uma visita guiada de funcionalidades que estão em disponibilidade geral.

> [!IMPORTANT]
> Crie uma [conta Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se ainda não criou uma.

## <a name="prepare-the-demo-environment"></a>Prepare o ambiente de demonstração

1. No seu navegador, vá à demonstração de [disponibilidade geral.](https://insights.timeseries.azure.com/demo)

1. Se solicitado, inscreva-se no explorador time series Insights utilizando as credenciais da sua conta Azure.

1. A página de tour rápida time Series Insights exibe. Selecione **Next** para iniciar o passeio rápido.

   [![Receção quickstart - selecione Next](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explore o ambiente de demonstração

1. O painel de **seleção time** exibe. Utilize este painel para selecionar um intervalo de tempo para visualizar.

   [![Painel de seleção de tempo](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Selecione um prazo e arraste-o para a região. Em seguida, selecione **Search**.

   [![Selecione um intervalo de tempo](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   O Time Series Insights apresenta uma visualização de gráfico para o intervalo de tempo especificado. Pode fazer várias ações dentro do gráfico de linha. Por exemplo, pode filtrar, pin, classificar e empilhar.

   Para voltar ao painel de **seleção**time, selecione a seta para baixo como mostrado:

   [![Gráfico](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Selecione **Adicionar** no **painel Termos** para adicionar um novo termo de pesquisa.

   [![Adicionar painel de termos de pesquisa](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. No gráfico, pode selecionar uma região, clicar com o botão direito na região e selecionar **Explorar Eventos**.

   [![Explorar Eventos](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Uma grelha dos seus dados brutos mostra da região que está a explorar.

   [![Explore eventos - vista de dados da grelha](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Selecione e filtre dados

1. Edite os seus termos para alterar os valores na tabela. Adicione outro termo para cruzar diferentes tipos de valores.

   [![Adicionar um termo](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Deixe a caixa da **série Filter** em branco para exibir todos os termos de pesquisa selecionados ou introduza um termo de filtro na caixa da **série Filter** para filtragem de séries improvisadas.

   [![Filtrar série](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Para o guia de introdução, introduza **Station5** para correlacionar entre si a temperatura e a pressão dessa estação.

Depois de concluir o guia de introdução, pode experimentar com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="clean-up-resources"></a>Limpar recursos

Agora que completou o tutorial, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** localize o seu grupo de recursos Azure Time Series Insights.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos nele) selecionando **Apagar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Está pronto para criar o seu próprio ambiente time series Insights:
> [!div class="nextstepaction"]
> [Planeie o seu ambiente do Time Series Insights](time-series-insights-environment-planning.md)
