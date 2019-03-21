---
title: Resolver problemas de ferramentas de análise de comportamento do utilizador no Azure Application Insights
description: Guia de resolução de problemas – análise da utilização de site e aplicação com o Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: eabc47c2acb33d8c6ee03477b5e8c7783edebbb7
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258785"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Resolver problemas de ferramentas de análise de comportamento do utilizador no Application Insights
Tem dúvidas sobre o [ferramentas de análise de comportamento do utilizador no Application Insights](usage-overview.md): [Utilizadores, sessões, eventos](usage-segmentation.md), [funis](usage-funnels.md), [fluxos de utilizador](usage-flows.md), [retenção](usage-retention.md), ou coortes? Seguem-se algumas respostas.

## <a name="counting-users"></a>Contagem de utilizadores
**As ferramentas de análise de comportamento do utilizador mostram que meu aplicativo tem um usuário/sessão, mas sei que meu aplicativo tem muitos utilizadores/sessões. Como posso corrigir essas contagens incorretas?**

Todos os eventos de telemetria no Application Insights tem um [ID de utilizador anónimo](../../azure-monitor/app/data-model-context.md) e uma [ID de sessão](../../azure-monitor/app/data-model-context.md) como duas das suas propriedades padrão. Por predefinição, todas as ferramentas de análise de utilização de contagem de utilizadores e sessões com base nestes IDs de. Se essas propriedades padrão não estiverem a ser preenchidas com IDs exclusivos para cada utilizador e a sessão da sua aplicação, verá uma contagem incorreta de utilizadores e sessões nas ferramentas de análise de utilização.

Se estiver a monitorizar uma aplicação web, a solução mais simples é adicionar o [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) à sua aplicação e certifique-se de que o fragmento de script é carregado em cada página que pretende monitorizar. O JavaScript SDK automaticamente gera um utilizador anónimo e IDs de sessão, em seguida, preenche a eventos de telemetria com estes IDs de à medida que são enviados da sua aplicação.

Se estiver a monitorizar um serviço da web (sem interface do usuário), [criar um inicializador de telemetria que povoa as propriedades de ID de ID e uma sessão de utilizador anónimo](usage-send-user-context.md) , de acordo com noções de seu serviço de utilizadores únicos e sessões.

Se estiver a enviar seu aplicativo [autenticado IDs de utilizador](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), pode contar com base no usuário autenticado IDs na ferramenta de utilizadores. Na lista pendente "Mostrar", escolha "Utilizadores autenticados".

As ferramentas de análise de comportamento do utilizador não são atualmente suportam contagem de utilizadores ou sessões com base nas propriedades que não seja o ID de utilizador anónimo, o ID de utilizador autenticado ou o ID de sessão.

## <a name="naming-events"></a>Eventos de nomenclatura
**Meu aplicativo tem milhares de vista de página diferentes e nomes de eventos personalizados. É difícil distinguir entre eles, e as ferramentas de análise de comportamento do utilizador, muitas vezes, deixar de responder. Como posso corrigir esses problemas de nomenclatura?**

Vista de página e nomes de eventos personalizados são utilizados em toda as ferramentas de análise de comportamento do utilizador. Eventos de nomenclatura bem é fundamental para retirar valor dessas ferramentas. O objetivo é um equilíbrio entre ter nomes muito poucos, bem genéricos ("botão clicado") e ter nomes de demasiados, excessivamente específicos ("botão Editar clicado em http:\//www.contoso.com/index").

Para fazer alterações para a vista de página e a sua aplicação está a enviar de nomes de evento personalizado, terá de alterar o código-fonte e Reimplementar a sua aplicação. **Telemetria de todos os dados no Application Insights são armazenados durante 90 dias e não não possível eliminar**, por isso, as alterações que fizer para nomes de eventos irão demorar 90 dias para totalmente do manifesto. Durante 90 dias depois de efetuar alterações de nome, ambos os nomes antigos e novos eventos irão aparecer na sua telemetria, então, ajustar consultas e comunicar dentro de suas equipes, em conformidade.

Se a sua aplicação estiver a enviar demasiados nomes de vistas de página, verifique se estes nomes de vistas de página são especificadas manualmente no código ou se estiver a ser enviados automaticamente pelo Application Insights JavaScript SDK:

* Se os nomes de exibição de página são especificados manualmente no código utilizando o [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), altere o nome para ser menos específicas. Evite erros comuns, como colocar o URL em nome da vista de página. Em alternativa, utilize o parâmetro de URL do `trackPageView` API. Mova outros detalhes do nome de exibição da página para propriedades personalizadas.

* Se o Application Insights JavaScript SDK está a enviar automaticamente os nomes de vista de página, pode alterar os títulos dos suas páginas ou mudar para enviar manualmente os nomes de vista de página. O SDK envia os [title](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como o nome da vista de página, por predefinição. Pode alterar os títulos de ser mais gerais, mas o cuidado de SEO e outros impactos que poderia ter esta alteração. Especificar manualmente a vista de página nomes com o `trackPageView` API substitui os nomes de recolhidas automaticamente, para que conseguiu enviar nomes mais gerais na telemetria, sem alterar os títulos de página.   

Se a aplicação está a enviar demasiados nomes de evento personalizado, altere o nome no código para ser menos específicas. Novamente, evite colocar URLs e outras por página ou informações dinâmicas nos nomes de evento personalizado diretamente. Em vez disso, mover esses detalhes para as propriedades personalizadas do evento personalizado com o `trackEvent` API. Por exemplo, em vez de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, sugerimos que algo como `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do ferramentas de análise de comportamento do utilizador](usage-overview.md)

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

