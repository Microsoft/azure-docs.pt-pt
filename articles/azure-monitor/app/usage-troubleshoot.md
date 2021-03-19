---
title: Ferramentas de análise de utilizadores de resolução de problemas - Azure Application Insights
description: Guia de resolução de problemas - analisar o uso do site e da aplicação com o Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 02d1bd9d204d88ba90218b1254c66ac0da80be85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87323507"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Resolução de problemas ferramentas de análise de comportamento do utilizador em Insights de Aplicação
Tem dúvidas sobre as ferramentas de análise de comportamento do [utilizador em Insights de Aplicação](usage-overview.md): [Utilizadores, Sessões,](usage-segmentation.md) [Eventos, Funis,](usage-funnels.md) [Fluxos de Utilizador,](usage-flows.md) [Retenção](usage-retention.md)ou Cohorts? Aqui estão algumas respostas.

## <a name="counting-users"></a>Contando utilizadores
**As ferramentas de análise de comportamento do utilizador mostram que a minha aplicação tem um utilizador/sessão, mas sei que a minha aplicação tem muitos utilizadores/sessões. Como posso corrigir estas contagens incorretas?**

Todos os eventos de telemetria em Application Insights têm um [ID de utilizador anónimo](./data-model-context.md) e um ID de [sessão](./data-model-context.md) como duas das suas propriedades padrão. Por padrão, todas as ferramentas de análise de uso contam os utilizadores e sessões com base nestes IDs. Se estas propriedades padrão não estiverem a ser preenchidas com IDs únicos para cada utilizador e sessão da sua aplicação, verá uma contagem incorreta de utilizadores e sessões nas ferramentas de análise de uso.

Se estiver a monitorizar uma aplicação web, a solução mais fácil é adicionar o [SDK JavaScript da aplicação](./javascript.md) e certificar-se de que o corte de script está carregado em cada página que pretende monitorizar. O JavaScript SDK gera automaticamente iDs de utilizador e de sessão anónimos, em seguida, povoa eventos de telemetria com estes IDs à medida que são enviados a partir da sua aplicação.

Se estiver a monitorizar um serviço web (sem interface de utilizador), [crie um inicializador de telemetria que povoa as propriedades anónimas do ID do utilizador e do ID](usage-send-user-context.md) de sessão de acordo com as noções do seu serviço de utilizadores e sessões únicas.

Se a sua aplicação estiver a enviar [IDs de utilizador autenticados,](./api-custom-events-metrics.md#authenticated-users)pode contar com base em IDs de utilizador autenticados na ferramenta Utilizadores. No dropdown "Show", escolha "utilizadores autenticados".

As ferramentas de análise de comportamento do utilizador não suportam atualmente a contagem de utilizadores ou sessões com base em propriedades que não o ID do utilizador anónimo, iD autenticado do utilizador ou ID de sessão.

## <a name="naming-events"></a>Eventos de Nomeação
**A minha aplicação tem milhares de visualizações de página diferentes e nomes de eventos personalizados. É difícil distinguir entre eles, e as ferramentas de análise de comportamento do utilizador muitas vezes tornam-se sem resposta. Como posso resolver estes problemas de nomeação?**

A visualização da página e os nomes personalizados do evento são usados em todas as ferramentas de análise de comportamento do utilizador. Nomear bem os eventos é fundamental para obter valor destas ferramentas. O objetivo é um equilíbrio entre ter muito poucos nomes, demasiado genéricos ("Button clicado") e ter demasiados nomes demasiado específicos ("Editar botão clicado em http: \/ /www.contoso.com/index").

Para efetuar quaisquer alterações à visualização da página e nomes personalizados do evento que a sua aplicação está a enviar, tem de alterar o código fonte da sua aplicação e redistribuir. **Todos os dados de telemetria em Application Insights são armazenados durante 90 dias e não podem ser eliminados**, pelo que as alterações que fizer aos nomes dos eventos levarão 90 dias a manifestar-se totalmente. Durante os 90 dias após a realização de alterações de nome, tanto os nomes antigos como os novos nomes de eventos aparecerão na sua telemetria, por isso ajuste as consultas e comunique-se dentro das suas equipas, em conformidade.

Se a sua aplicação estiver a enviar demasiados nomes de visualização de página, verifique se estes nomes de visualização da página são especificados manualmente em código ou se estão a ser enviados automaticamente pela Aplicação Insights JavaScript SDK:

* Se os nomes de visualização da página forem especificados manualmente em código utilizando a [ `trackPageView` API,](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)altere o nome para ser menos específico. Evite erros comuns como colocar o URL no nome da vista da página. Em vez disso, utilize o parâmetro URL da `trackPageView` API. Mova outros detalhes do nome da vista da página para propriedades personalizadas.

* Se o SDK JavaScript da Aplicação estiver a enviar automaticamente nomes de visualização de página, pode alterar os títulos das suas páginas ou mudar para enviar manualmente nomes de visualização de página. O SDK envia o [título](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como o nome de visualização da página, por padrão. Pode mudar os seus títulos para ser mais geral, mas esteja atento ao SEO e a outros impactos que esta mudança pode ter. Especificar manualmente os nomes de visualização da página com a `trackPageView` API substitui os nomes recolhidos automaticamente, para que possa enviar nomes mais gerais em telemetria sem alterar os títulos de página.   

Se a sua aplicação estiver a enviar demasiados nomes personalizados para eventos, altere o nome no código para ser menos específico. Mais uma vez, evite colocar URLs e outras informações por página ou informações dinâmicas nos nomes de eventos personalizados diretamente. Em vez disso, mova estes detalhes para propriedades personalizadas do evento personalizado com a `trackEvent` API. Por exemplo, em vez de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` sugerirmos algo `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` como.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral das ferramentas de análise de comportamento do utilizador](usage-overview.md)

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

