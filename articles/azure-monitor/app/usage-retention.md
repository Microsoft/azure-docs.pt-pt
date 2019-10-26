---
title: Análise de retenção de usuário para aplicativos Web com insights Aplicativo Azure | Microsoft docs
description: Quantos usuários retornam ao seu aplicativo?
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 5f5f6235354adc565815ac2eab0a1c774267102d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899420"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de usuário para aplicativos Web com Application Insights

O recurso de retenção no [insights aplicativo Azure](../../azure-monitor/app/app-insights-overview.md) ajuda você a analisar quantos usuários retornam ao seu aplicativo e com que frequência eles executam tarefas específicas ou atingem metas. Por exemplo, se você executar um site do jogo, poderá comparar os números de usuários que retornam ao site depois de perder um jogo com o número que retorna após o vencedor. Esse conhecimento pode ajudá-lo a melhorar sua experiência de usuário e sua estratégia de negócios.

## <a name="get-started"></a>Introdução

Se você ainda não vê dados na ferramenta de retenção no portal de Application Insights, [saiba como começar a usar as ferramentas de uso](usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/usage-retention/retention.png)

1. A barra de ferramentas permite que os usuários criem novos relatórios de retenção, abram os relatórios de retenção existentes, salvem o relatório de retenção atual ou salvem como, revertam as alterações feitas nos relatórios salvos, atualize os dados no relatório, compartilhem relatórios por email ou link direto e acesse a documentação Web. 
2. Por padrão, a retenção mostra todos os usuários que fizeram qualquer coisa que voltaram e faziam qualquer outra coisa em um período. Você pode selecionar uma combinação diferente de eventos para restringir o foco em atividades específicas do usuário.
3. Adicione um ou mais filtros nas propriedades. Por exemplo, você pode se concentrar em usuários em um país ou região específica. Clique em **Atualizar** depois de definir os filtros. 
4. O gráfico de retenção geral mostra um resumo da retenção de usuário durante o período de tempo selecionado. 
5. A grade mostra o número de usuários retidos de acordo com o construtor de consultas no #2. Cada linha representa um coorte de usuários que executaram qualquer evento no período de tempo mostrado. Cada célula na linha mostra quantos desses coorte retornaram pelo menos uma vez em um período posterior. Alguns usuários podem retornar em mais de um período. 
6. Os cartões de informações mostram os cinco principais eventos de início e os cinco principais eventos retornados para fornecer aos usuários uma melhor compreensão do relatório de retenção. 

![Focalização do mouse de retenção](./media/usage-retention/hover.png)

Os usuários podem focalizar células na ferramenta de retenção para acessar o botão de análise e dicas de ferramenta que explicam o que significa a célula. O botão análise leva os usuários para a ferramenta de análise com uma consulta preenchida previamente para gerar usuários da célula. 

## <a name="use-business-events-to-track-retention"></a>Usar eventos de negócios para acompanhar a retenção

Para obter a análise de retenção mais útil, meça os eventos que representam atividades de negócios significativas. 

Por exemplo, muitos usuários podem abrir uma página em seu aplicativo sem executar o jogo que ele exibe. Controlar apenas as exibições de página, portanto, forneceria uma estimativa imprecisa de quantas pessoas retornam para jogar o jogo depois de se beneficiarem anteriormente. Para obter uma visão clara do retorno de jogadores, seu aplicativo deve enviar um evento personalizado quando um usuário realmente é reproduzido.  

É uma prática recomendada codificar eventos personalizados que representem as principais ações de negócios e usá-los para sua análise de retenção. Para capturar o resultado do jogo, você precisa escrever uma linha de código para enviar um evento personalizado para Application Insights. Se você escrevê-lo no código da página da Web ou no node. JS, ele terá esta aparência:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código do servidor ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Passos seguintes
- Para habilitar as experiências de uso, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já enviar eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários usam seu serviço.
    - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de usuário](usage-send-user-context.md)


