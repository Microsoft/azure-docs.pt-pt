---
title: Solucionar problemas de ferramentas do User Analytics-Aplicativo Azure insights
description: Guia de solução de problemas-analisando o uso de sites e aplicativos com o Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aa540cdaef1af3016d87ab93768ceb30802cef0e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432284"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Solucionar problemas das ferramentas de análise de comportamento do usuário no Application Insights
Tem dúvidas sobre as [ferramentas de análise de comportamento do usuário no Application insights](usage-overview.md): [usuários, sessões, eventos](usage-segmentation.md), [funils](usage-funnels.md), [fluxos dos usuários](usage-flows.md), [retenção](usage-retention.md)ou coortes? Aqui estão algumas respostas.

## <a name="counting-users"></a>Contando usuários
**As ferramentas de análise de comportamento do usuário mostram que meu aplicativo tem um usuário/sessão, mas sei que meu aplicativo tem muitos usuários/sessões. Como corrigir essas contagens incorretas?**

Todos os eventos de telemetria no Application Insights têm uma [ID de usuário anônimo](../../azure-monitor/app/data-model-context.md) e uma [ID de sessão](../../azure-monitor/app/data-model-context.md) como duas de suas propriedades padrão. Por padrão, todas as ferramentas de análise de uso contam usuários e sessões com base nessas IDs. Se essas propriedades padrão não estiverem sendo preenchidas com IDs exclusivas para cada usuário e sessão do seu aplicativo, você verá uma contagem incorreta de usuários e sessões nas ferramentas de análise de uso.

Se você estiver monitorando um aplicativo Web, a solução mais fácil é adicionar o [SDK Application insights JavaScript](../../azure-monitor/app/javascript.md) ao seu aplicativo e verificar se o trecho de script está carregado em cada página que você deseja monitorar. O SDK do JavaScript gera automaticamente IDs de usuário e de sessão anônimas e, em seguida, preenche os eventos de telemetria com essas IDs à medida que elas são enviadas do seu aplicativo.

Se você estiver monitorando um serviço Web (sem interface do usuário), [crie um inicializador de telemetria que popula a ID de usuário anônimo e as propriedades de ID de sessão](usage-send-user-context.md) de acordo com as noções do seu serviço de usuários e sessões exclusivos.

Se seu aplicativo estiver enviando [IDs de usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), você poderá contar com base nas IDs de usuário autenticado na ferramenta usuários. Na lista suspensa "Mostrar", escolha "usuários autenticados".

As ferramentas de análise de comportamento do usuário atualmente não dão suporte à contagem de usuários ou sessões com base em propriedades diferentes da ID de usuário anônimo, ID de usuário autenticado ou ID de sessão.

## <a name="naming-events"></a>Eventos de nomenclatura
**Meu aplicativo tem milhares de exibições de página e nomes de evento personalizados diferentes. É difícil distinguir entre eles, e as ferramentas de análise de comportamento do usuário geralmente não respondem. Como corrigir esses problemas de nomenclatura?**

A exibição de página e os nomes de eventos personalizados são usados em todas as ferramentas de análise de comportamento do usuário. A nomenclatura de eventos é essencial para obter o valor dessas ferramentas. A meta é um equilíbrio entre ter um número muito pequeno, muito genérico ("botão clicado") e ter nomes muito específicos ("botão de edição clicado em http:\//www.contoso.com/index").

Para fazer alterações no modo de exibição de página e nomes de evento personalizados que seu aplicativo está enviando, você precisa alterar o código-fonte do aplicativo e reimplantá-lo. **Todos os dados de telemetria no Application insights são armazenados por 90 dias e não podem ser excluídos**, portanto, as alterações feitas nos nomes dos eventos levarão 90 dias para o manifesto completo. Por 90 dias depois de fazer alterações de nome, os nomes de evento antigo e novo aparecerão na telemetria, portanto, ajuste as consultas e comunique-se em suas equipes de acordo.

Se seu aplicativo estiver enviando um número excessivo de nomes de exibição de página, verifique se esses nomes de exibição de página são especificados manualmente no código ou se estão sendo enviados automaticamente pelo SDK Application Insights JavaScript:

* Se os nomes de exibição de página forem especificados manualmente no código usando a [API`trackPageView`](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), altere o nome para que ele seja menos específico. Evite erros comuns, como colocar a URL no nome da exibição de página. Em vez disso, use o parâmetro URL da API `trackPageView`. Mova outros detalhes do nome da exibição de página para propriedades personalizadas.

* Se o SDK do Application Insights JavaScript estiver enviando automaticamente nomes de exibição de página, você poderá alterar os títulos de suas páginas ou alternar para o envio manual de nomes de exibição de página. Por padrão, o SDK envia o [título](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como o nome da exibição de página. Você pode alterar seus títulos para serem mais gerais, mas lembre-se de SEO e outros impactos que essa alteração pode ter. Especificar manualmente nomes de exibição de página com a API de `trackPageView` substitui os nomes coletados automaticamente, para que você possa enviar nomes mais gerais na telemetria sem alterar os títulos de página.   

Se seu aplicativo estiver enviando muitos nomes de evento personalizado, altere o nome no código para ser menos específico. Novamente, evite colocar URLs e outras informações por página ou dinâmicas diretamente nos nomes dos eventos personalizados. Em vez disso, mova esses detalhes para as propriedades personalizadas do evento personalizado com a API `trackEvent`. Por exemplo, em vez de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, sugerimos algo como `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral das ferramentas de análise de comportamento do usuário](usage-overview.md)

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

