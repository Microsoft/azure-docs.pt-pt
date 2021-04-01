---
title: 'Quickstart: Azure Time Series Insights Explorer - Azure Time Series Insights | Microsoft Docs'
description: Saiba como começar com o Azure Time Series Insights Explorer. Visualize grandes volumes de dados IoT e as principais características do seu ambiente.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/30/2020
ms.openlocfilehash: dd5ed52187a7cd23142b59e20c91c6d125946a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91613786"
---
# <a name="quickstart-explore-azure-time-series-insights-gen1"></a>Quickstart: Explore Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este quickstart da Azure Time Series Insights Explorer ajuda-o a começar com o Azure Time Series Insights num ambiente de demonstração gratuito. Neste quickstart, você aprende a usar o seu navegador web para visualizar grandes volumes de dados IoT e funcionalidades chave de turismo que estão geralmente disponíveis agora.

Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica como explorar e analisar milhares de milhões de eventos IoT simultaneamente. Dá-lhe uma visão global dos seus dados para que possa validar rapidamente a sua solução IoT e evitar tempos de inatividade dispendiosos para dispositivos críticos da missão. Azure Time Series Insights ajuda-o a descobrir tendências ocultas, anomalias no local e a realizar análises de causa de raiz em tempo real.

Para uma flexibilidade adicional, pode adicionar Azure Time Series Insights a uma aplicação pré-existente através das suas [poderosas APIs de REST](./concepts-query-overview.md) e [do cliente SDK](https://github.com/microsoft/tsiclient). Pode utilizar as APIs para armazenar, consultar e consumir dados de séries de tempo numa aplicação do cliente à sua escolha. Também pode utilizar o cliente SDK para adicionar componentes de UI à sua aplicação existente.

Este Azure Time Series Insights Explorer quickstart oferece uma visita guiada às funcionalidades.

> [!IMPORTANT]
> Crie uma [conta Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se ainda não tiver criado uma.

## <a name="prepare-the-demo-environment"></a>Prepare o ambiente de demonstração

1. No seu navegador, vá à [demonstração da Gen1.](https://insights.timeseries.azure.com/demo)

1. Se solicitado, inscreva-se no Azure Time Series Insights Explorer utilizando as credenciais da sua conta Azure.

1. A página de visita rápida Azure Time Series Insights. Selecione **Next** para iniciar o passeio rápido.

   [![Quickstart welcome - selecione Next](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explore o ambiente de demonstração

1. O **painel de seleção time** apresenta. Utilize este painel para selecionar um intervalo de tempo para visualizar.

   [![Painel de seleção de tempo](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Selecione um período de tempo e arraste-o na região. Em seguida, **selecione Pesquisar**.

   [![Selecione um intervalo de tempo](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Azure Time Series Insights apresenta uma visualização de gráfico para o período de tempo especificado. Pode fazer várias ações dentro da tabela de linhas. Por exemplo, pode filtrar, pin, classificar e empilhar.

   Para voltar ao **painel de seleção time,** selecione a seta para baixo como mostrado:

   [![Gráfico](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. **Selecione Adicionar** no **painel termos** para adicionar um novo termo de pesquisa.

   [![Adicionar painel de termos de pesquisa](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. No gráfico, pode selecionar uma região, clicar com o botão direito na região e selecionar **Explorar Eventos**.

   [![Explorar Eventos](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Uma grelha dos seus dados brutos mostra da região que está a explorar.

   [![Explore eventos - vista de dados da grelha](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Selecione e filtra dados

1. Edite os seus termos para alterar os valores na tabela. Adicione mais um termo para correlacionar diferentes tipos de valores.

   [![Adicionar um termo](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Deixe a caixa **da série Filter** em branco para exibir todos os termos de pesquisa selecionados ou introduza um termo de filtro na caixa da série **Filter** para filtragem de séries improvisadas.

   [![Filtrar série](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Para o guia de introdução, introduza **Station5** para correlacionar entre si a temperatura e a pressão dessa estação.

Depois de concluir o guia de introdução, pode experimentar com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="clean-up-resources"></a>Limpar os recursos

Agora que completou o tutorial, limpe os recursos que criou:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos**, localize o seu grupo de recursos Azure Time Series Insights.
1. Ou elimina todo o grupo de recursos (e todos os recursos contidos no mesmo) selecionando **Eliminar** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

* Está pronto para criar o seu próprio ambiente Azure Time Series Insights. Leia [o plano do seu ambiente Azure Time Series Insights](time-series-insights-environment-planning.md).
