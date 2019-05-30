---
title: 'Início rápido: Explorador do Time Series Insights do Azure | Documentos da Microsoft'
description: Este guia de introdução mostra-lhe como começar com o Explorador do Azure Time Series Insights no seu browser para visualizar grandes volumes de dados de IoT. Visita guiada pelas principais funcionalidades num ambiente de demonstração.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 17aee2555a0d0364ca5eb8e260ae10432a19bce6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244047"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Início rápido: Explorar o Azure Time Series Insights

Este guia de introdução do Explorador do Azure Time Series Insights ajuda-o a começar a utilizar o Time Series Insights num ambiente de demonstração gratuito. Através do mesmo, irá aprender a utilizar o seu navegador da web para visualizar grandes volumes de IoT dados e Tour pelos recursos-chave que estão agora geralmente disponíveis.

O Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido que simplifica a explorar e analisar milhares de milhões de eventos de IoT em simultâneo. Ele fornece uma vista global dos seus dados para que possa rapidamente validar a sua solução de IoT e evitar o período de indisponibilidade dispendioso para dispositivos fundamentais. O Azure Time Series Insights ajuda-o a descobrir tendências ocultas, detetar anomalias e realizar análises de causa raiz em tempo real.

Para obter flexibilidade adicional, pode adicionar o Azure Time Series Insights para uma aplicação preexistente através de sua poderosa [REST APIs](./time-series-insights-update-tsq.md) e [SDK de cliente](./tutorial-create-tsi-sample-spa.md). Pode utilizar as APIs para armazenar, consulta e consumir dados de séries de tempo num aplicativo de cliente da sua preferência. Também pode utilizar o SDK do cliente para adicionar componentes da interface do Usuário para seu aplicativo existente.

Este guia de introdução do Explorador do Time Series Insights oferece um tour guiado de funcionalidades que estão agora geralmente disponíveis.

## <a name="prepare-the-demo-environment"></a>Preparar o ambiente de demonstração

1. Criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se ainda não Criei uma.

1. No seu browser, vá para o [demonstração de disponibilidade geral](https://insights.timeseries.azure.com/demo).

1. Se lhe for pedido, inicie sessão no Explorador do Time Series Insights utilizando as credenciais da conta do Azure.

1. A página de visita guiada rápida do Time Series Insights é apresentada. Selecione **seguinte** para começar a visita guiada rápida.

   [![Selecione avançar](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explorar o ambiente de demonstração

1. O **painel de seleção de tempo** apresenta. Utilize este painel para selecionar um intervalo de tempo para visualizar.

   [![Painel de seleção de tempo](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Selecione um intervalo de tempo e arraste-o na região. Em seguida, selecione **pesquisa**.

   [![Selecione um intervalo de tempo](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   O Time Series Insights apresenta uma visualização de gráfico para o intervalo de tempo especificado. Pode realizar diversas ações no gráfico de linhas. Por exemplo, pode filtrar, afixar, ordenar e pilha.

   Para voltar para o **painel de seleção de tempo**, selecione a seta para baixo, conforme mostrado:

   [![Gráfico](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Selecione **Add** no **painel de termos** para adicionar um novo termo de pesquisa.

   [![Adicionar item](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. No gráfico, pode selecionar uma região, clicar com o botão direito na região e selecionar **Explorar Eventos**.

   [![Explorar eventos](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Uma grade de dados não processados apresenta da região que está a explorar.

   [![Vista de grelha](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Selecionar e filtrar dados

1. Edite os termos para alterar os valores no gráfico. Adicione outro termo para diferentes tipos de valores a correlação cruzada.

   [![Adicionar um termo](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Introduza um termo de filtro na **filtrar série** caixa para filtrar séries improvised. Para o guia de introdução, introduza **Station5** para correlacionar entre si a temperatura e a pressão dessa estação.

   [![Filtrar série](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Depois de concluir o guia de introdução, pode experimentar com o conjunto de dados de exemplo para criar visualizações diferentes.

## <a name="next-steps"></a>Passos Seguintes

Está pronto para criar seu próprio ambiente do Time Series Insights:
> [!div class="nextstepaction"]
> [Planeie o seu ambiente do Time Series Insights](time-series-insights-environment-planning.md)
