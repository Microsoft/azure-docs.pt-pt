---
title: Analisar padrões de navegação do usuário com Fluxos dos Usuários no Aplicativo Azure insights | Microsoft docs
description: Analise como os usuários navegam entre as páginas e os recursos do seu aplicativo Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b4ebdfa692bb8727556a37698e3a280f40bc1b0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899493"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analisar padrões de navegação do usuário com Fluxos dos Usuários no Application Insights

![Ferramenta de Fluxos dos Usuários de Application Insights](./media/usage-flows/00001-flows.png)

A ferramenta Fluxos dos Usuários visualiza como os usuários navegam entre as páginas e os recursos do seu site. É ótimo responder a perguntas como:

* Como os usuários navegam para fora de uma página no seu site?
* O que os usuários clicam em uma página em seu site?
* Onde estão os locais que os usuários mais fazem a rotatividade de seu site?
* Há lugares onde os usuários repetim a mesma ação várias vezes?

A ferramenta de Fluxos dos Usuários é iniciada em uma exibição de página inicial, evento personalizado ou exceção que você especificar. Devido a esse evento inicial, Fluxos dos Usuários mostra os eventos que ocorreram antes e depois durante as sessões do usuário. Linhas de espessura variável mostram quantas vezes cada caminho foi seguido pelos usuários. Nós de **sessão especial iniciados** mostram onde os nós subsequentes começaram uma sessão. Nós de **sessão encerrados** mostram quantos usuários não enviaram exibições de página ou eventos personalizados após o nó anterior, destacando onde os usuários provavelmente saíram do seu site.

> [!NOTE]
> O recurso de Application Insights deve conter exibições de página ou eventos personalizados para usar a ferramenta Fluxos dos Usuários. [Saiba como configurar seu aplicativo para coletar exibições de página automaticamente com o SDK Application insights JavaScript](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Comece escolhendo um evento inicial

![Escolha um evento inicial para Fluxos dos Usuários](./media/usage-flows/00002-flows-initial-event.png)

Para começar a responder perguntas com a ferramenta Fluxos dos Usuários, escolha uma exibição de página inicial, evento personalizado ou exceção para servir como o ponto de partida para a visualização:

1. Clique no link no título o **que os usuários fazem depois de...?** , ou clique no botão **Editar** .
2. Selecione uma exibição de página, evento personalizado ou exceção na lista suspensa de **eventos inicial** .
3. Clique em **criar grafo**.

A coluna "Step 1" da visualização mostra o que os usuários fizeram com mais frequência logo após o evento inicial, ordenados de cima para baixo, do mais para o menos frequente. A "etapa 2" e as colunas subsequentes mostram o que os usuários fizeram depois, criando uma imagem de todas as formas que os usuários navegaram pelo site.

Por padrão, a ferramenta de Fluxos dos Usuários só obtém aleatoriamente as últimas 24 horas de exibições de página e eventos personalizados do seu site. Você pode aumentar o intervalo de tempo e alterar o equilíbrio entre desempenho e precisão para amostragem aleatória no menu Editar.

Se algumas das exibições de página, eventos personalizados e exceções não forem relevantes para você, clique no **X** nos nós que você deseja ocultar. Depois de selecionar os nós que você deseja ocultar, clique no botão **criar grafo** abaixo da visualização. Para ver todos os nós que você ocultou, clique no botão **Editar** e examine a seção **eventos excluídos** .

Se estiverem faltando exibições de página ou eventos personalizados que você espera ver na visualização:

* Verifique a seção **eventos excluídos** no menu **Editar** .
* Use os botões de adição em **outros** nós para incluir eventos menos frequentes na visualização.
* Se o modo de exibição de página ou evento personalizado esperado for enviado com pouca frequência pelos usuários, tente aumentar o intervalo de tempo da visualização no menu **Editar** .
* Verifique se a exibição de página, o evento personalizado ou a exceção esperada está configurado para ser coletado pelo SDK do Application Insights no código-fonte do seu site. [Saiba mais sobre como coletar eventos personalizados.](../../azure-monitor/app/api-custom-events-metrics.md)

Se você quiser ver mais etapas na visualização, use os menus suspensos **etapas anteriores** e **próximas etapas** acima da visualização.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Depois de visitar uma página ou recurso, onde os usuários vão e o que eles clicam?

![Use Fluxos dos Usuários para entender onde os usuários clicam](./media/usage-flows/00003-flows-one-step.png)

Se o evento inicial for uma exibição de página, a primeira coluna ("etapa 1") da visualização será uma maneira rápida de entender o que os usuários fizeram imediatamente depois de visitar a página. Tente abrir o site em uma janela ao lado da visualização Fluxos dos Usuários. Compare suas expectativas de como os usuários interagem com a página na lista de eventos na coluna "Step 1". Geralmente, um elemento de interface do usuário na página que parece insignificante para sua equipe pode estar entre os mais usados na página. Pode ser um ótimo ponto de partida para aprimoramentos de design em seu site.

Se o evento inicial for um evento personalizado, a primeira coluna mostrará o que os usuários fizeram logo após a execução dessa ação. Assim como nas exibições de página, considere se o comportamento observado dos usuários corresponde às metas e às expectativas da sua equipe. Se o evento inicial selecionado for "Adicionar item ao carrinho de compras", por exemplo, procure ver se "ir para checkout" e "compra concluída" aparecerão na visualização logo em seguida. Se o comportamento do usuário for diferente de suas expectativas, use a visualização para entender como os usuários estão se tornando "interceptados" pelo design atual do seu site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Onde estão os locais que os usuários mais fazem a rotatividade de seu site?

Observe os nós de **sessão encerrados** que aparecem em alto em uma coluna na visualização, especialmente no início de um fluxo. Isso significa que muitos usuários provavelmente se transformaram em seu site depois de seguir o caminho anterior de páginas e interações de interface do usuário. Às vezes, a rotatividade é esperada-depois de concluir uma compra em um site de comércio eletrônico, por exemplo, mas geralmente a rotatividade é um sinal de problemas de design, baixo desempenho ou outros problemas com seu site que podem ser melhorados.

Tenha em mente que os nós **encerrados da sessão** são baseados apenas na telemetria coletada por esse Application insights recurso. Se Application Insights não receber telemetria para determinadas interações de usuário, os usuários ainda poderão ter interagindo com seu site dessas maneiras depois que a ferramenta de Fluxos dos Usuários disser que a sessão terminou.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Há lugares onde os usuários repetim a mesma ação várias vezes?

Procure um modo de exibição de página ou evento personalizado que seja repetido por muitos usuários nas etapas subsequentes na visualização. Isso geralmente significa que os usuários estão executando ações repetitivas em seu site. Se você encontrar repetição, pense em alterar o design do seu site ou adicionar uma nova funcionalidade para reduzir a repetição. Por exemplo, adicionar funcionalidade de edição em massa se você encontrar usuários executando ações repetitivas em cada linha de um elemento de tabela.

## <a name="common-questions"></a>Perguntas comuns

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>O evento inicial representa a primeira vez que o evento aparece em uma sessão ou sempre que ele aparece em uma sessão?

O evento inicial na visualização representa apenas a primeira vez que um usuário enviou essa exibição de página ou evento personalizado durante uma sessão. Se os usuários puderem enviar o evento inicial várias vezes em uma sessão, a coluna "Step 1" mostrará apenas como os usuários se comportam após a *primeira* instância do evento inicial, e não todas as instâncias.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Alguns dos nós na minha visualização são de alto nível. Por exemplo, um nó que simplesmente diz "botão clicado". Como posso dividi-lo em nós mais detalhados?

Use as opções **dividir por** no menu **Editar** :

1. Escolha o evento que você deseja dividir no menu de **eventos** .
2. Escolha uma dimensão no menu **dimensão** . Por exemplo, se você tiver um evento chamado "botão clicado", tente uma propriedade personalizada chamada "nome do botão".

## <a name="next-steps"></a>Passos seguintes

* [Visão geral de uso](usage-overview.md)
* [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
* [Retenção](usage-retention.md)
* [Adicionando eventos personalizados ao seu aplicativo](../../azure-monitor/app/api-custom-events-metrics.md)