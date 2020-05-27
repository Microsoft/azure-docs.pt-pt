---
title: Analisar a retenção de utilizadores de aplicações web com insights de aplicação Azure
description: Quantos utilizadores regressam à sua aplicação?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 654dba968ba4efec1c30c94082726da04059adc0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797739"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de utilizadores para aplicações web com Insights de Aplicação

A funcionalidade de retenção no [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ajuda-o a analisar quantos utilizadores regressam à sua aplicação e com que frequência executam tarefas específicas ou alcançam objetivos. Por exemplo, se executar esquelético, pode comparar o número de utilizadores que regressam ao site depois de perder um jogo com o número que regressa após ganhar. Este conhecimento pode ajudá-lo a melhorar tanto a sua experiência de utilizador como a sua estratégia de negócio.

## <a name="get-started"></a>Introdução

Se ainda não vir dados na ferramenta de retenção no portal Application Insights, [aprenda a começar com as ferramentas](usage-overview.md)de utilização .

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/usage-retention/retention.png)

1. A barra de ferramentas permite que os utilizadores criem novos relatórios de retenção, abram relatórios de retenção existentes, guardem o relatório de retenção atual ou economizem alterações feitas em relatórios guardados, atualizardados sobre o relatório, partilhar relatórios via e-mail ou link direto, e aceder à página de documentação. 
2. Por padrão, a retenção mostra todos os utilizadores que fizeram alguma coisa e depois voltaram e fizeram qualquer outra coisa durante um período. Pode selecionar diferentes combinações de eventos para limitar o foco em atividades específicas do utilizador.
3. Adicione um ou mais filtros nas propriedades. Por exemplo, pode focar-se nos utilizadores de um determinado país ou região. Clique em **Atualizar** depois de definir os filtros. 
4. O gráfico de retenção global mostra um resumo da retenção do utilizador durante o período de tempo selecionado. 
5. A grelha mostra o número de utilizadores retidos de acordo com o construtor de consultas em #2. Cada linha representa uma coorte de utilizadores que realizaram qualquer evento no período de tempo mostrado. Cada célula da fila mostra quantos dessa coorte regressaram pelo menos uma vez num período posterior. Alguns utilizadores podem regressar em mais de um período. 
6. Os cartões de insights mostram os cinco melhores eventos de início, e os cinco melhores eventos devolvidos para dar aos utilizadores uma melhor compreensão do seu relatório de retenção. 

![Paira do rato de retenção](./media/usage-retention/hover.png)

Os utilizadores podem pairar sobre as células na ferramenta de retenção para aceder ao botão de análise e dicas de ferramentas explicando o que a célula significa. O botão Analytics leva os utilizadores à ferramenta Analytics com uma consulta pré-povoada para gerar utilizadores a partir da célula. 

## <a name="use-business-events-to-track-retention"></a>Use eventos empresariais para rastrear a retenção

Para obter a análise de retenção mais útil, meça eventos que representem atividades empresariais significativas. 

Por exemplo, muitos utilizadores podem abrir uma página na sua aplicação sem jogar o jogo que exibe. Rastrear apenas as visualizações da página forneceria, portanto, uma estimativa imprecisa de quantas pessoas voltam a jogar o jogo depois de o terem apreciado anteriormente. Para obter uma imagem clara dos jogadores que regressam, a sua aplicação deve enviar um evento personalizado quando um utilizador realmente joga.  

É uma boa prática codificar eventos personalizados que representam ações empresariais fundamentais, e usá-los para a sua análise de retenção. Para capturar o resultado do jogo, precisa de escrever uma linha de código para enviar um evento personalizado para Application Insights. Se o escrever no código da página web ou no Node.JS, parece:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código do servidor ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre a escrita de eventos personalizados.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)


## <a name="next-steps"></a>Passos seguintes
- Para permitir experiências de utilização, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [visualizações de páginas.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Se já envia eventos personalizados ou visualizações de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/platform/workbooks-overview.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)


