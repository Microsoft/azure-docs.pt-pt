---
title: Analise a retenção de utilizadores de aplicativos web com Azure Application Insights
description: Quantos utilizadores regressam à sua aplicação?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37de27e8dc707133595b71b39e45bdec554289e5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583391"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de utilizadores para aplicações web com Insights de Aplicações

A funcionalidade de retenção no [Azure Application Insights](./app-insights-overview.md) ajuda-o a analisar quantos utilizadores regressam à sua aplicação e com que frequência executam tarefas específicas ou alcançam objetivos. Por exemplo, se executares um site de jogo, podes comparar os números de utilizadores que regressam ao site depois de perderes um jogo com o número que regressa depois de ganhares. Este conhecimento pode ajudá-lo a melhorar tanto a sua experiência de utilizador como a sua estratégia de negócio.

## <a name="get-started"></a>Introdução

Se ainda não vir dados na ferramenta de retenção no portal Application Insights, [aprenda a começar com as ferramentas de utilização.](usage-overview.md)

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/usage-retention/retention.png)

1. A barra de ferramentas permite que os utilizadores criem novos relatórios de retenção, abram relatórios de retenção existentes, guardem o relatório de retenção atual ou guardem como, revertam as alterações feitas aos relatórios guardados, atualização de dados do relatório, partilhem relatórios via e-mail ou link direto e acedam à página de documentação. 
2. Por padrão, a retenção mostra todos os utilizadores que fizeram alguma coisa e depois voltaram e fizeram qualquer outra coisa durante um período. Pode selecionar diferentes combinações de eventos para reduzir o foco em atividades específicas do utilizador.
3. Adicione um ou mais filtros nas propriedades. Por exemplo, pode focar-se em utilizadores num determinado país ou região. Clique em **Update** after setting the filters. 
4. O gráfico geral de retenção mostra um resumo da retenção do utilizador durante o período de tempo selecionado. 
5. A grelha mostra o número de utilizadores retidos de acordo com o construtor de consultas em #2. Cada linha representa um grupo de utilizadores que realizaram qualquer evento no período de tempo mostrado. Cada célula da fila mostra quantas desta coorte regressaram pelo menos uma vez num período posterior. Alguns utilizadores podem regressar em mais de um período. 
6. Os cartões de insights mostram os cinco melhores eventos iniciantes, e os cinco principais eventos devolvidos para dar aos utilizadores uma melhor compreensão do seu relatório de retenção. 

![Pairar do rato de retenção](./media/usage-retention/hover.png)

Os utilizadores podem pairar sobre as células na ferramenta de retenção para aceder ao botão de análise e dicas de ferramentas explicando o que a célula significa. O botão Analytics leva os utilizadores à ferramenta Analytics com uma consulta pré-povoada para gerar utilizadores a partir da célula. 

## <a name="use-business-events-to-track-retention"></a>Use eventos de negócios para acompanhar a retenção

Para obter a análise de retenção mais útil, meça eventos que representam atividades comerciais significativas. 

Por exemplo, muitos utilizadores podem abrir uma página na sua aplicação sem jogar o jogo que exibe. Seguir apenas as visualizações da página forneceria uma estimativa imprecisa de quantas pessoas voltam a jogar o jogo depois de o desfrutarem anteriormente. Para obter uma imagem clara dos jogadores que regressam, a sua aplicação deve enviar um evento personalizado quando um utilizador realmente joga.  

É uma boa prática codificar eventos personalizados que representem ações de negócio chave, e usá-los para a sua análise de retenção. Para capturar o resultado do jogo, precisa de escrever uma linha de código para enviar um evento personalizado para Application Insights. Se o escrever no código da página web ou em Node.JS, é o seguinte:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código do servidor ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre a escrita de eventos personalizados.](./api-custom-events-metrics.md#trackevent)


## <a name="next-steps"></a>Passos seguintes
- Para ativar experiências de utilização, comece a enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) ou [vistas de página.](./api-custom-events-metrics.md#page-views)
- Se já envia eventos personalizados ou vistas de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos de Utilizador](usage-flows.md)
    - [Livros](../visualize/workbooks-overview.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)

