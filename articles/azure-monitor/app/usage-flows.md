---
title: Azure Application Insights User Flows analisa fluxos de navegação
description: Analise como os utilizadores navegam entre as páginas e as funcionalidades da sua aplicação web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309274"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analise os padrões de navegação dos utilizadores com fluxos de utilizador em insights de aplicação

![Ferramenta De Fluxos de Utilizador de Insights de Aplicação](./media/usage-flows/flows.png)

A ferramenta User Flows visualiza como os utilizadores navegam entre as páginas e as funcionalidades do seu site. É ótimo para responder a perguntas como:

* Como é que os utilizadores navegam longe de uma página no seu site?
* O que os utilizadores clicam numa página do seu site?
* Onde estão os locais que os utilizadores mais agitam do seu site?
* Existem locais onde os utilizadores repetem a mesma ação repetidamente?

A ferramenta Fluxos de Utilizador começa a partir de uma vista inicial de página, evento personalizado ou exceção que especifica. Dado este evento inicial, o User Flows mostra os eventos que aconteceram antes e depois durante as sessões do utilizador. Linhas de espessura variada mostram quantas vezes cada caminho foi seguido pelos utilizadores. Os nós da **Special Session Started** mostram onde os nós subsequentes começaram uma sessão. Os nós **terminados** mostram quantos utilizadores não enviaram visualizações de página ou eventos personalizados após o nó anterior, realçando onde os utilizadores provavelmente deixaram o seu site.

> [!NOTE]
> O seu recurso Application Insights deve conter visualizações de página ou eventos personalizados para utilizar a ferramenta Fluxos de Utilizador. [Saiba como configurar a sua aplicação para recolher as vistas da página automaticamente com o App Insights JavaScript SDK](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Comece por escolher um evento inicial

![Escolha um evento inicial para Fluxos de Utilizador](./media/usage-flows/initial-event.png)

Para começar a responder a perguntas com a ferramenta Fluxos de Utilizador, escolha uma vista inicial de página, evento personalizado ou exceção para servir como ponto de partida para a visualização:

1. Clique no link no **o que os utilizadores fazem depois...?** 
2. Selecione uma vista de página, evento personalizado ou exceção do **dropdown inicial** do evento.
3. Clique **em Criar gráfico.**

A coluna "Passo 1" da visualização mostra o que os utilizadores faziam mais frequentemente logo após o evento inicial, ordenou de cima para baixo da maioria para menos frequente. O "Passo 2" e as colunas subsequentes mostram o que os utilizadores fizeram depois, criando uma imagem de todas as formas como os utilizadores navegaram através do seu site.

Por predefinição, a ferramenta Fluxos de Utilizador amostra aleatoriamente apenas as últimas 24 horas de visualizações da página e evento personalizado a partir do seu site. Pode aumentar o intervalo de tempo e alterar o equilíbrio de desempenho e precisão para amostragem aleatória no menu Editar.

Se algumas das visualizações da página, eventos personalizados e exceções não forem relevantes para si, clique no **X** nos nós que pretende esconder. Uma vez selecionado os nós que pretende ocultar, clique no botão **'Criar gráfico'** abaixo da visualização. Para ver todos os nós que ocultou, clique no botão **Editar** e, em seguida, olhe para a secção **de eventos excluídos.**

Se faltam visualizações de página ou eventos personalizados que espera ver na visualização:

* Consulte a secção **de eventos excluídos** no menu **Editar.**
* Utilize os botões plus em **Outros** nós para incluir eventos menos frequentes na visualização.
* Se a visualização da página ou evento personalizado que espera for enviado com frequência pelos utilizadores, tente aumentar o intervalo de tempo da visualização no menu **Editar.**
* Certifique-se de que a visualização da página, evento personalizado ou exceção que espera que seja configurada para ser recolhida pelo Application Insights SDK no código fonte do seu site. [Saiba mais sobre a recolha de eventos personalizados.](./api-custom-events-metrics.md)

Se quiser ver mais passos na visualização, utilize os **passos anteriores** e **os próximos passos** para descer acima da visualização.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Depois de visitar uma página ou funcionalidade, para onde vão os utilizadores e o que clicam?

![Utilize fluxos de utilizador para entender onde os utilizadores clicam](./media/usage-flows/one-step.png)

Se o seu evento inicial for uma visualização de página, a primeira coluna ("Passo 1") da visualização é uma forma rápida de entender o que os utilizadores fizeram imediatamente após visitarem a página. Tente abrir o seu site numa janela junto à visualização dos Fluxos de Utilizador. Compare as suas expectativas de como os utilizadores interagem com a página com a lista de eventos na coluna "Passo 1". Muitas vezes, um elemento de UI na página que parece insignificante para a sua equipa pode estar entre os mais utilizados na página. Pode ser um ótimo ponto de partida para melhorias de design no seu site.

Se o seu evento inicial for um evento personalizado, a primeira coluna mostra o que os utilizadores fizeram logo após realizar essa ação. Tal como acontece com as visualizações da página, considere se o comportamento observado dos seus utilizadores corresponde aos objetivos e expectativas da sua equipa. Se o seu evento inicial selecionado for "Item adicionado ao Carrinho de Compras", por exemplo, procure ver se "Ir a Checkout" e "Complete Purchase" aparecem na visualização pouco depois. Se o comportamento do utilizador for diferente das suas expectativas, use a visualização para entender como os utilizadores estão a ficar "presos" pelo design atual do seu site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Onde estão os locais que os utilizadores mais agitam do seu site?

Observe os nós terminados de **sessão** que aparecem altos numa coluna na visualização, especialmente no início de um fluxo. Isto significa que muitos utilizadores provavelmente arrancaram do seu site depois de seguirem o caminho anterior de páginas e interações de UI. Às vezes é esperado churn - depois de concluir uma compra em um site de eCommerce, por exemplo - mas geralmente churn é um sinal de problemas de design, mau desempenho, ou outros problemas com o seu site que podem ser melhorados.

Tenha em mente que os nós **De Sessão Terminadas** baseiam-se apenas na telemetria recolhida por este recurso Application Insights. Se o Application Insights não receber telemetria para determinadas interações do utilizador, os utilizadores poderão ainda ter interagido com o seu site dessa forma após a ferramenta Fluxos de Utilizador dizer que a sessão terminou.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Existem locais onde os utilizadores repetem a mesma ação repetidamente?

Procure uma visualização de página ou evento personalizado que seja repetido por muitos utilizadores em etapas subsequentes na visualização. Isto geralmente significa que os utilizadores estão a realizar ações repetitivas no seu site. Se encontrar repetição, pense em alterar o design do seu site ou adicionar uma nova funcionalidade para reduzir a repetição. Por exemplo, adicionar funcionalidade de edição a granel se encontrar utilizadores a realizar ações repetitivas em cada linha de um elemento de tabela.

## <a name="common-questions"></a>Perguntas comuns

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>O evento inicial representa a primeira vez que o evento aparece numa sessão, ou em qualquer momento que apareça numa sessão?

O evento inicial na visualização representa apenas a primeira vez que um utilizador enviou a visualização da página ou evento personalizado durante uma sessão. Se os utilizadores puderem enviar o evento inicial várias vezes numa sessão, então a coluna "Passo 1" só mostra como os utilizadores se comportam após a *primeira* instância do evento inicial, nem todas as instâncias.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Alguns dos nós da minha visualização são de alto nível. Por exemplo, um nó que diz apenas "Botão Clicado". Como posso decompô-lo em nós mais detalhados?

Utilize a **Divisão por** opções no menu **Editar:**

1. Escolha o evento que pretende quebrar no menu **Evento.**
2. Escolha uma dimensão no menu **Dimension.** Por exemplo, se tiver um evento chamado "Button Clicked", experimente uma propriedade personalizada chamada "Nome do botão".

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do uso](usage-overview.md)
* [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
* [Retenção](usage-retention.md)
* [Adicionar eventos personalizados à sua app](./api-custom-events-metrics.md)

