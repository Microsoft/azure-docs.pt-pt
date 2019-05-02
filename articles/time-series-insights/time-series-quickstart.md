---
title: 'Início rápido: Explorador do Time Series Insights do Azure | Documentos da Microsoft'
description: Este guia de introdução mostra como começar com o explorador do Azure Time Series Insights no seu browser para visualizar grandes volumes de dados de IoT. Visita guiada pelas principais funcionalidades num ambiente de demonstração.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696963"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Início rápido: Explorar o Azure Time Series Insights

Um Explorador de início rápido é fornecido para começar a utilizar o Azure Time Series Insights num ambiente de demonstração gratuito. Através do mesmo, aprenderá como usar o seu navegador da web para visualizar grandes volumes de IoT dados e Tour pelos recursos-chave atualmente em disponibilidade geral.

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a explorar e analisar milhares de milhões de eventos de IoT em simultâneo. Ele fornece uma vista global dos seus dados, que lhe permite validar rapidamente a sua solução de IoT e evitar o período de indisponibilidade dispendioso para dispositivos fundamentais. Através do Azure Time Series Insights, pode descobrir tendências ocultas, detetar anomalias e realizar análises de causa raiz em tempo real.

Para obter flexibilidade adicional, o Azure Time Series Insights podem ser adicionado a uma aplicação já existente por meio de sua poderosa [REST APIs](./time-series-insights-update-tsq.md) e [SDK de cliente](./tutorial-create-tsi-sample-spa.md). As APIs permitem-lhe armazenar dados de séries de tempo de consulta e consumam dados de séries de tempo num aplicativo de cliente da sua preferência. Também pode optar por utilizar o SDK do cliente para adicionar componentes da interface do Usuário para seu aplicativo existente.

O Time Series Insights explorer é um tour guiado atualmente possui em geral disponibilidade.

## <a name="prepare-the-demo-environment"></a>Preparar o ambiente de demonstração

1. Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se um não tenha sido criado.

1. No seu browser, navegue para o [demonstração de disponibilidade geral](https://insights.timeseries.azure.com/demo).

1. Se lhe for pedido, inicie sessão no Explorador do Time Series Insights com as suas credenciais de conta do Azure.

1. A página de visita guiada rápida do Time Series Insights é apresentada. Clique em **Seguinte** para começar a visita guiada rápida.

   [![Clique em seguinte](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explorar o ambiente de demonstração

1. O **painel de seleção de tempo** é apresentado. Utilize este painel para selecionar um intervalo de tempo para visualizar.

   [![Painel de seleção de tempo](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Clique e arraste na região e, em seguida, clique no botão **Pesquisa**.

   [![Selecione um intervalo de tempo](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   O Time Series Insights apresenta uma visualização de gráfico para o intervalo de tempo especificado. Pode realizar diversas ações no gráfico de linhas, como a filtragem, afixação, ordenação e pilha.

   Para voltar para o **Painel de seleção de tempo**, clique na seta para baixo, conforme mostrado:

   [![Gráfico](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Clique em **Adicionar** no **Painel de termos** para adicionar um novo termo de pesquisa.

   [![Adicionar item](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. No gráfico, pode selecionar uma região, clicar com o botão direito na região e selecionar **Explorar Eventos**.

   [![Explorar eventos](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   É apresentada uma grelha dos dados não processados na região que está a explorar:

   [![Vista de grelha](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Selecionar e filtrar dados

1. Edite os termos para alterar os valores no gráfico e adicione outro termo para diferentes tipos de valores correlacionados entre si:

   [![Adicionar um termo](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Introduza um termo de filtro no **filtrar série...**  caixa para filtrar séries improvised. Para o guia de introdução, introduza **Station5** para correlacionar entre si a temperatura e a pressão dessa estação.

   [![Filtrar série](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Depois de concluir o guia de introdução, pode experimentar com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="next-steps"></a>Passos Seguintes

Está pronto para criar o seu próprio ambiente do Time Series Insights:
> [!div class="nextstepaction"]
> [Planeie o seu ambiente do Time Series Insights](time-series-insights-environment-planning.md)
