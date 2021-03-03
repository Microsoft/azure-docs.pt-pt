---
title: Azure API Management Diagnostic e resolver problemas
description: Saiba como resolver problemas com a sua API em Gestão API Azure com a ferramenta Diagnose and Solve no portal Azure.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652407"
---
# <a name="azure-api-management-diagnostics-overview"></a>Visão geral de diagnósticos de gestão da API da Azure

Quando constrói e gere uma API na Azure API Management, quer estar preparado para quaisquer problemas que possam surgir, desde 404 erros não encontrados a 502 erros de gateway. A API Management Diagnostics é uma experiência inteligente e interativa para ajudá-lo a resolver problemas na sua API publicada na APIM sem necessidade de configuração. Quando se deteta de problemas com as suas APIs publicadas, a API Management Diagnostics aponta o que está errado e orienta-o para a informação certa para resolver rapidamente os problemas e resolver o problema.

Embora esta experiência seja mais útil quando está a ter problemas com a sua API nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis para que possa analisar.

## <a name="open-api-management-diagnostics"></a>Diagnósticos abertos de gestão da API

Para aceder aos Diagnósticos de Gestão da API, navegue para a sua instância de serviço de Gestão API no [portal Azure.](https://portal.azure.com) Na navegação à esquerda, selecione **Diagnosticar e resolver problemas.**

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="A screenshot mostra como navegar para diagnósticos.":::



## <a name="intelligent-search"></a>Pesquisa inteligente

Pode pesquisar os seus problemas ou problemas na barra de pesquisa no topo da página. A pesquisa também ajuda a encontrar as ferramentas que podem ajudar a resolver problemas ou resolver os seus problemas. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="imagem de pesquisa inteligente.":::


## <a name="troubleshooting-categories"></a>Categorias de resolução de problemas

Pode resolver problemas em categorias. Questões comuns relacionadas com o desempenho da API, gateway, políticas de API e nível de serviço podem ser analisadas em cada categoria. Cada categoria também fornece verificações de diagnóstico mais específicas. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="screenshot da visão geral da categoria.":::


### <a name="availability-and-performance"></a>Disponibilidade e desempenho

Consulte os problemas de disponibilidade e desempenho da API nesta categoria. Depois de selecionar este azulejo de categoria, verá que alguns controlos comuns são recomendados numa interface interativa. Clique em cada verificação para mergulhar profundamente nas especificidades de cada edição. A verificação também lhe fornecerá um gráfico que mostra o desempenho da API e um resumo dos problemas de desempenho. Por exemplo, o seu serviço API pode ter tido um erro de 5xx e tempo limite na última hora no backend. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Screenshot 1 da verificação de interface interativa.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Screenshot 2 da verificação da Interface Interativa.":::

### <a name="api-policies"></a>Políticas da API

Esta categoria deteta erros e notifica-o das suas questões políticas. 

Uma interface interativa semelhante guia-o para as métricas de dados para ajudá-lo a resolver a configuração das suas políticas de API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="screenshot do azulejo da categoria API Políticas.":::

### <a name="gateway-performance"></a>Desempenho de gateways 

Para pedidos de gateway ou respostas ou quaisquer erros de 4xx ou 5xx no seu gateway, utilize esta categoria para monitorizar e resolver problemas. Da mesma forma, aproveite a interface interativa para mergulhar profundamente na área específica que deseja verificar para o seu desempenho no gateway da API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="screenshot do azulejo da categoria de desempenho gateway.":::

### <a name="service-upgrade"></a>Atualização de serviço

Esta categoria verifica qual o nível de serviço (SKU) que está a utilizar e lembra-lhe para atualizar para evitar quaisquer problemas que possam estar relacionados com esse nível. A mesma interface interativa ajuda-o a aprofundar-se com mais gráficos e um resultado de verificação de resumo. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="screenshot do azulejo da categoria de upgrade de serviço.":::

## <a name="search-documentation"></a>Pesquisar documentação

Para além das ferramentas de diagnóstico e resolução de problemas, pode procurar documentação de resolução de problemas relacionada com o seu problema. Depois de executar os diagnósticos no seu serviço, selecione **Documentação de Pesquisa** na interface interativa. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="screenshot 1 de como usar a função de Documentação de Pesquisa.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="screenshot 2 de como usar a Documentação de Pesquisa.":::


## <a name="next-steps"></a>Passos seguintes

* Utilize também [análises API](howto-use-analytics.md) para analisar o uso e desempenho das APIs. 
* Pretende resolver problemas com problemas de Aplicações Web com Diagnósticos? Leia [aqui](../app-service/overview-diagnostics.md)
* Aproveite os diagnósticos para verificar os problemas dos Serviços Azure Kubernetes. Ver [Diagnósticos em AKS](../aks/concepts-diagnostics.md)
* Publique as suas perguntas ou feedback no [UserVoice](https://feedback.azure.com/forums/248703-api-management) adicionando "[Diag]" no título.
