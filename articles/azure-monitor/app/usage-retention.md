---
title: Análise de retenção de utilizador para aplicações web com o Azure Application Insights | Documentos da Microsoft
description: Devolve o número de utilizadores à sua aplicação?
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bda79520dd86cc14161f6f22cd24feb2e35849ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372639"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de utilizador para aplicações web com o Application Insights

A funcionalidade de retenção no [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ajuda a analisar o número de utilizadores voltam à sua aplicação e a frequência com que realizar tarefas específicas ou atingir as metas. Por exemplo, se executar um site de jogo, pode comparar os números de utilizadores que retornam ao site após a perda de um jogo com o número que retornam após vencedoras. Esse conhecimento pode ajudar a melhorar sua experiência de utilizador e a sua estratégia de negócios.

## <a name="get-started"></a>Introdução

Se ainda não vir dados no portal do Application Insights, a ferramenta de retenção [Saiba como começar com as ferramentas de utilização](usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/usage-retention/retention.png)

1. Barra de ferramentas permite aos utilizadores criar novos relatórios de retenção, abra os relatórios existentes de retenção, guardar o relatório de retenção atual ou guardar como, reverter as alterações feitas para relatórios guardados, atualizar dados no relatório, partilhar o relatório por e-mail ou a ligação direta e acessar a documentação página. 
2. Por predefinição, a retenção mostra todos os utilizadores que fizeram nada, em seguida, voltaram e fizeram mais nada durante um período. Pode selecionar uma combinação diferente de eventos para restringir o foco nas atividades de utilizador específico.
3. Adicione um ou mais filtros de propriedades. Por exemplo, que se possa concentrar nos utilizadores de um determinado país ou região. Clique em **atualização** depois de definir os filtros. 
4. O gráfico de retenções totais mostra um resumo de retenção de utilizador entre o período de tempo selecionado. 
5. A grade mostra o número de utilizadores retidos, de acordo com o construtor de consultas no #2. Cada linha representa uma coorte de utilizadores que executou a qualquer evento no tempo período mostrado. Cada célula na linha mostra o número desse coorte devolvido pelo menos uma vez num período posterior. Alguns usuários podem devolver mais do que um período. 
6. Os cartões de informações mostram os principais eventos de início de cinco e principais eventos devolvidos cinco para dar aos usuários uma melhor compreensão do seu relatório de retenção. 

![Coloque o cursor de rato de retenção](./media/usage-retention/hover.png)

Os utilizadores podem Paire o rato sobre células sobre a ferramenta de retenção para acessar as dicas de botão e a ferramenta de análise que explica o que significa que a célula. Botão Analytics direciona os utilizadores para a ferramenta de análise com uma consulta preenchida previamente para gerar utilizadores da célula. 

## <a name="use-business-events-to-track-retention"></a>Usar eventos de negócios para controlar a retenção

Para obter a análise de retenção mais úteis, medir os eventos que representam as atividades de negócios significativas. 

Por exemplo, número de utilizadores que pode abrir uma página na sua aplicação sem jogar o jogo que é apresentado. Apenas as vistas de página de controlo, portanto, seria fornecer uma estimativa imprecisa de quantas pessoas retornam para jogar o jogo após anteriormente a desfrutar. Para obter uma visão clara de retornar leitores, seu aplicativo deve enviar um evento personalizado quando um usuário realmente toca.  

É boa prática eventos personalizados que representam as ações de negócios essenciais de código e utilizá-los para sua análise de retenção. Para capturar o resultado de jogo, terá de escrever uma linha de código para enviar um evento personalizado para o Application Insights. Se escrever o código de página da web ou em node. js, fica assim:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou, no código de servidor do ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Passos Seguintes
- Para permitir experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já tiver enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o seu serviço.
    - [Utilizadores, Sessões, Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)


