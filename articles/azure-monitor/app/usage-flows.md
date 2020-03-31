---
title: Analise os padrões de navegação dos utilizadores com fluxos de utilizadores em Insights de Aplicação Azure [ Microsoft docs
description: Analise a forma como os utilizadores navegam entre as páginas e as funcionalidades da sua aplicação web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a4a81a3e4c5759e444836162319abb97d83a4c74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671074"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analisar padrões de navegação do utilizador com fluxos de utilizador em Insights de Aplicação

![Ferramenta de fluxos de utilizadores de insights de aplicação](./media/usage-flows/00001-flows.png)

A ferramenta User Flows visualiza a forma como os utilizadores navegam entre as páginas e as funcionalidades do seu site. É ótimo responder a perguntas como:

* Como é que os utilizadores navegam longe de uma página no seu site?
* O que os utilizadores clicam numa página do seu site?
* Onde estão os lugares que os utilizadores mais churn do seu site?
* Há lugares onde os utilizadores repetem a mesma ação vezes sem conta?

A ferramenta User Flows começa a partir de uma visão inicial da página, evento personalizado ou exceção que especifica. Dado este evento inicial, o User Flows mostra os eventos que aconteceram antes e depois durante as sessões do utilizador. Linhas de espessura variada mostram quantas vezes cada caminho foi seguido pelos utilizadores. Sessão Especial Os acenos **começaram** a mostrar onde os nódosos seguintes começaram uma sessão. Os nós **da Sessão Final** mostram quantos utilizadores não enviaram visualizações de página ou eventos personalizados após o nó anterior, realçando onde os utilizadores provavelmente deixaram o seu site.

> [!NOTE]
> O seu recurso Application Insights deve conter visualizações de página ou eventos personalizados para utilizar a ferramenta User Flows. [Saiba como configurar a sua aplicação para recolher automaticamente as visualizações da página com os Insights de Aplicação JavaScript SDK](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Comece por escolher um evento inicial

![Escolha um evento inicial para Fluxos de Utilizador](./media/usage-flows/00002-flows-initial-event.png)

Para começar a responder a perguntas com a ferramenta User Flows, escolha uma visão inicial da página, evento personalizado ou exceção para servir como ponto de partida para a visualização:

1. Clique no link no **what dos utilizadores fazerem depois...?** **Edit**
2. Selecione uma vista de página, evento personalizado ou exceção do dropdown inicial do **evento.**
3. Clique **em Criar gráfico**.

A coluna "Passo 1" da visualização mostra o que os utilizadores fizeram com mais frequência logo após o evento inicial, ordenada de cima a baixo da maioria para o menos frequente. As colunas "Step 2" e posteriores mostram o que os utilizadores fizeram posteriormente, criando uma imagem de todas as formas como os utilizadores navegaram pelo seu site.

Por padrão, a ferramenta User Flows amostra aleatoriamente apenas as últimas 24 horas de visualizações da página e evento personalizado do seu site. Pode aumentar o intervalo de tempo e alterar o equilíbrio de desempenho e precisão para uma amostragem aleatória no menu Editar.

Se algumas das vistas da página, eventos personalizados e exceções não forem relevantes para si, clique no **X** nos nós que pretende esconder. Depois de selecionar os nós que pretende esconder, clique no botão **Criar** gráfico abaixo da visualização. Para ver todos os nós que escondeu, clique no botão **Editar** e, em seguida, veja a secção de **eventos excluídos.**

Se faltarem vistas de página ou eventos personalizados que espera ver na visualização:

* Verifique a secção **de eventos excluídos** no menu **Editar.**
* Use os botões plus em **outros** nódosos para incluir eventos menos frequentes na visualização.
* Se a visualização da página ou evento personalizado que espera ser enviada com pouca frequência pelos utilizadores, tente aumentar a gama de tempo da visualização no menu **Editar.**
* Certifique-se de que a visualização da página, evento personalizado ou exceção que espera estar configurada para ser recolhida pelo SDK de Insights de Aplicação no código fonte do seu site. [Saiba mais sobre a recolha de eventos personalizados.](../../azure-monitor/app/api-custom-events-metrics.md)

Se quiser ver mais passos na visualização, utilize os **passos anteriores** e os **passos seguintes** acima da visualização.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Depois de visitar uma página ou funcionalidade, para onde vão os utilizadores e no que clicam?

![Utilize fluxos de utilizador para entender onde os utilizadores clicam](./media/usage-flows/00003-flows-one-step.png)

Se o seu evento inicial for uma visão de página, a primeira coluna ("Passo 1") da visualização é uma forma rápida de entender o que os utilizadores fizeram imediatamente após a visita à página. Tente abrir o seu site numa janela ao lado da visualização dos Fluxos de Utilizador. Compare as suas expectativas de como os utilizadores interagem com a página com a lista de eventos na coluna "Passo 1". Muitas vezes, um elemento ui na página que parece insignificante para a sua equipa pode estar entre os mais utilizados na página. Pode ser um ótimo ponto de partida para melhorias de design no seu site.

Se o seu evento inicial for um evento personalizado, a primeira coluna mostra o que os utilizadores fizeram logo após a realização dessa ação. Tal como acontece com as vistas da página, considere se o comportamento observado dos seus utilizadores corresponde aos objetivos e expectativas da sua equipa. Se o seu evento inicial selecionado for "Add Item to Shopping Cart", por exemplo, procure ver se "Ir para checkout" e "Complete D'Purchase" aparecem na visualização pouco depois. Se o comportamento do utilizador for diferente das suas expectativas, use a visualização para entender como os utilizadores estão a ficar "presos" pelo design atual do seu site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Onde estão os lugares que os utilizadores mais churn do seu site?

Observe os nós **de Sessão Terminados** que aparecem altos numa coluna na visualização, especialmente no início de um fluxo. Isto significa que muitos utilizadores provavelmente se retiraram do seu site depois de seguirem o caminho anterior das páginas e das interações de UI. Por vezes, espera-se que churn - depois de concluir uma compra num site de eCommerce, por exemplo - mas geralmente churn é um sinal de problemas de design, mau desempenho ou outros problemas com o seu site que podem ser melhorados.

Tenha em mente que os nós **sessão terminados** são baseados apenas na telemetria recolhida por este recurso Application Insights. Se o Application Insights não receber telemetria para determinadas interações do utilizador, os utilizadores ainda podem ter interagido com o seu site dessa forma após a ferramenta User Flows dizer que a sessão terminou.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Há lugares onde os utilizadores repetem a mesma ação vezes sem conta?

Procure uma visualização de página ou evento personalizado que seja repetido por muitos utilizadores em etapas subsequentes na visualização. Isto geralmente significa que os utilizadores estão a realizar ações repetitivas no seu site. Se encontrar repetição, pense em mudar o design do seu site ou adicionar uma nova funcionalidade para reduzir a repetição. Por exemplo, adicionar funcionalidade de edição a granel se encontrar utilizadores que realizem ações repetitivas em cada linha de um elemento de tabela.

## <a name="common-questions"></a>Perguntas comuns

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>O evento inicial representa a primeira vez que o evento aparece numa sessão, ou em qualquer altura em que aparece numa sessão?

O evento inicial sobre a visualização representa apenas a primeira vez que um utilizador enviou essa visualização ou evento personalizado durante uma sessão. Se os utilizadores puderem enviar o evento inicial várias vezes numa sessão, então a coluna "Passo 1" apenas mostra como os utilizadores se comportam após a *primeira* instância do evento inicial, e não todas as instâncias.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Alguns dos nódosos da minha visualização são muito altos. Por exemplo, um nó que diz apenas "Botão Clicado". Como posso decompartá-lo em nós mais detalhados?

Utilize a **Divisão por** opções no menu **Editar:**

1. Escolha o evento que pretende quebrar no menu **evento.**
2. Escolha uma dimensão no menu **Dimensão.** Por exemplo, se tiver um evento chamado "Button Clicked", experimente uma propriedade personalizada chamada "Nome do botão".

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do uso](usage-overview.md)
* [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
* [Retenção](usage-retention.md)
* [Adicionar eventos personalizados à sua aplicação](../../azure-monitor/app/api-custom-events-metrics.md)
