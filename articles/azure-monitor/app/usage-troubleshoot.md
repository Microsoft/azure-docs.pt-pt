---
title: Ferramentas de análise de utilizadores de resolução de problemas - Insights de aplicação Azure
description: Guia de resolução de problemas - analisando o uso do site e da aplicação com insights de aplicação.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670921"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Ferramentas de análise de comportamento do utilizador em Insights de Aplicação
Tem dúvidas sobre as ferramentas de análise de comportamento do utilizador em Insights de [Aplicação](usage-overview.md): [Utilizadores, Sessões, Eventos,](usage-segmentation.md) [Funis, Fluxos](usage-funnels.md) [de Utilizador,](usage-flows.md) [Retenção](usage-retention.md)ou Cohorts? Aqui estão algumas respostas.

## <a name="counting-users"></a>Contagem de Utilizadores
**As ferramentas de análise de comportamento do utilizador mostram que a minha aplicação tem um utilizador/sessão, mas sei que a minha aplicação tem muitos utilizadores/sessões. Como posso corrigir estas contagens incorretas?**

Todos os eventos de telemetria em Application Insights têm um [ID de utilizador anónimo](../../azure-monitor/app/data-model-context.md) e um ID de [sessão](../../azure-monitor/app/data-model-context.md) como duas das suas propriedades padrão. Por padrão, todas as ferramentas de utilização contam utilizadores e sessões com base nestes IDs. Se estas propriedades padrão não estiverem a ser povoadas com IDs únicos para cada utilizador e sessão da sua aplicação, verá uma contagem incorreta de utilizadores e sessões nas ferramentas de análise de uso.

Se estiver a monitorizar uma aplicação web, a solução mais fácil é adicionar os Insights de [Aplicação JavaScript SDK](../../azure-monitor/app/javascript.md) à sua aplicação e certificar-se de que o snippet do script está carregado em cada página que pretende monitorizar. O JavaScript SDK gera automaticamente iDs de utilizador e sessão anónimos, e depois povoa eventos de telemetria com estes IDs à medida que são enviados da sua aplicação.

Se estiver a monitorizar um serviço web (sem interface de utilizador), [crie um inicializador de telemetria que povoe as propriedades de ID do utilizador anónimo e](usage-send-user-context.md) de id de sessão de acordo com as noções do seu serviço de utilizadores e sessões únicas.

Se a sua aplicação estiver a enviar [iDs autenticados,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)pode contar com base em iDs autenticados de utilizadores na ferramenta Utilizadores. No dropdown "Show", escolha "Utilizadores autenticados".

As ferramentas de análise de comportamento do utilizador não suportam atualmente a contagem de utilizadores ou sessões com base em propriedades que não o ID do utilizador anónimo, o ID autenticado do utilizador ou o ID da sessão.

## <a name="naming-events"></a>Nomeação de Eventos
**A minha aplicação tem milhares de diferentes nomes de página e nomes de eventos personalizados. É difícil distinguir entre eles, e as ferramentas de análise do comportamento do utilizador muitas vezes tornam-se sem resposta. Como posso resolver estes problemas de nomeação?**

A visualização da página e os nomes personalizados dos eventos são usados em todas as ferramentas de análise de comportamento do utilizador. Nomear bem os acontecimentos é fundamental para obter valor destas ferramentas. O objetivo é um equilíbrio entre ter nomes demasiado poucos e demasiado genéricos ("Botão clicado") e\/ter demasiados nomes demasiado específicos ("Editar botão clicado em http: /www.contoso.com/index").

Para efazer quaisquer alterações na visualização da página e nomes personalizados de eventos que a sua aplicação está a enviar, precisa de alterar o código fonte da sua aplicação e recolocar. Todos os dados de **telemetria em Application Insights são armazenados durante 90 dias e não podem ser eliminados**, pelo que as alterações que fizer aos nomes dos eventos demorarão 90 dias a manifestar-se totalmente. Durante os 90 dias após a alteração do nome, tanto os nomes antigos como os novos eventos aparecerão na sua telemetria, por isso ajuste as consultas e comunique dentro das suas equipas, em conformidade.

Se a sua aplicação estiver a enviar demasiados nomes de visualização de páginas, verifique se estes nomes de visualização de página são especificados manualmente em código ou se estão a ser enviados automaticamente pelo JavaScript SDK da aplicação:

* Se os nomes da página forem especificados manualmente em código utilizando a [ `trackPageView` API,](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)mude o nome para ser menos específico. Evite erros comuns como colocar o URL em nome da vista da página. Em vez disso, utilize `trackPageView` o parâmetro URL da API. Mova outros detalhes da visualização da página para propriedades personalizadas.

* Se o Formulário de Aplicação JavaScript SDK estiver a enviar automaticamente os nomes de visualização da página, pode alterar os títulos das suas páginas ou mudar para enviar manualmente os nomes de visualização da página. O SDK envia o [título](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como nome de visualização da página, por padrão. Pode mudar os seus títulos para ser mais geral, mas esteja atento ao SEO e outros impactos que esta mudança pode ter. Especificando manualmente os nomes de visualização da página com a `trackPageView` API sobrepõe-se aos nomes recolhidos automaticamente, para que possa enviar mais nomes gerais em telemetria sem alterar os títulos da página.   

Se a sua aplicação estiver a enviar demasiados nomes de eventos personalizados, altere o nome no código para ser menos específico. Mais uma vez, evite colocar URLs e outras informações por página ou dinâmicas nos nomes de eventos personalizados diretamente. Em vez disso, mova estes detalhes para `trackEvent` propriedades personalizadas do evento personalizado com a API. Por exemplo, `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`em vez de `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`sugerirmos algo como.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral das ferramentas de análise do comportamento do utilizador](usage-overview.md)

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

