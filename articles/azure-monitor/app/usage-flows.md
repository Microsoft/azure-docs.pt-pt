---
title: Analisar padrões de navegação do usuário com fluxos de utilizador no Azure Application Insights | Documentos da Microsoft
description: Analise a forma como os utilizadores navegam entre as páginas e funcionalidades da sua aplicação web.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 91274fad4e56c69777333c81ea3b32dccdcf64ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373301"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analisar padrões de navegação do usuário com fluxos de utilizador no Application Insights

![Ferramenta de fluxos de utilizador de informações da aplicação](./media/usage-flows/00001-flows.png)

A ferramenta de fluxos de utilizador visualiza de forma que os utilizadores navegam entre as páginas e funcionalidades do seu site. É ótimo para responder a perguntas como:

* Como os utilizadores para navegar para fora de uma página no seu site?
* O que os utilizadores clicam numa página no seu site?
* Onde estão os locais que os utilizadores mais alterações a partir do seu site?
* Existem locais onde os utilizadores repetir a mesma ação repetidamente?

A ferramenta de fluxos de utilizador é iniciada a partir de uma vista de página inicial, o evento personalizado ou a exceção que especificar. Tendo em conta este evento inicial, fluxos de utilizador mostra os eventos que aconteceram antes e depois durante sessões de utilizador. Linhas de espessura variada mostram quantas vezes cada caminho foi seguido por utilizadores. Especial **iniciar sessão** nós mostram onde os nós subsequentes começaram a uma sessão. **Ter terminado de sessão** nós mostram o número de utilizadores enviados não existem vistas de página ou eventos personalizados depois do nó anterior, realce em que os usuários provavelmente deixado de seu site.

> [!NOTE]
> Recurso do Application Insights pode conter vistas de página ou eventos personalizados para usar a ferramenta fluxos de utilizador. [Saiba como configurar a sua aplicação para recolher as vistas de página automaticamente com o Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Comece escolhendo um evento inicial

![Escolher um evento inicial para fluxos de utilizador](./media/usage-flows/00002-flows-initial-event.png)

Para começar a responder a perguntas com a ferramenta fluxos de utilizador, selecione uma vista de página inicial, o evento personalizado ou a exceção para ser usado como o ponto de partida para a visualização:

1. Clique na ligação no **o, os utilizadores fazem depois...?**  título ou clique nas **editar** botão.
2. Selecione uma vista de página, evento personalizado ou exceção a partir da **evento inicial** lista pendente.
3. Clique em **gráfico de criar**.

A coluna "Passo 1" da visualização mostra o que os utilizadores fizeram com mais frequência apenas após o evento inicial, ordenados de cima para baixo de mais para menos frequentes. O "Passo 2" e próximas colunas mostram o que os usuários fizeram por esse motivo, a criação de uma imagem de todos os utilizadores de formas fizeram navegação através do seu site.

Por predefinição, a ferramenta fluxos de utilizador aleatoriamente copia apenas as últimas 24 horas de vistas de página e eventos personalizados do seu site. Pode aumentar o intervalo de tempo e alterar o equilíbrio de desempenho e precisão para a amostragem aleatória no menu Editar.

Se algumas das exceções, eventos personalizados e vistas de página não são relevantes para si, clique nas **X** em nós que pretende ocultar. Depois de selecionar os nós que pretende ocultar, clique nas **gráfico de criar** botão abaixo a visualização. Para ver todos os nós que ocultadas, clique a **editar** botão, em seguida, examinar a **excluídos eventos** secção.

Se as vistas de página ou eventos personalizados estão em falta que pretende ver na visualização:

* Verifique os **excluídos eventos** secção a **editar** menu.
* Utilize os botões de adição **outros** nós para incluir menos freqüente eventos na visualização.
* Se a vista de página ou eventos personalizados que espera é enviado com pouca frequência por utilizadores, experimente aumentar o intervalo de tempo da visualização no **editar** menu.
* Certifique-se ver de página, evento personalizado, ou exceção esperado está configurado para ser recolhidos pelo SDK do Application Insights no código-fonte do seu site. [Saiba mais sobre a recolha de eventos personalizados.](../../azure-monitor/app/api-custom-events-metrics.md)

Se quiser ver mais passos na visualização, utilize o **passos anteriores** e **passos** listas pendentes acima da visualização.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Depois de visitar uma página ou funcionalidade, que os usuários acessam e o que eles clique?

![Utilizar fluxos de utilizador para compreender onde os utilizadores em](./media/usage-flows/00003-flows-one-step.png)

Se o seu evento inicial é uma vista de página, a primeira coluna ("passo 1") da visualização é uma forma rápida de compreender o que os utilizadores que fiz imediatamente após a visitar a página. Tente abrir seu site numa janela junto a visualização de fluxos de utilizador. Compare as suas expectativas de como os utilizadores interagem com a página para a lista de eventos na coluna "Passo 1". Muitas vezes, um elemento de interface do Usuário na página que parece insignificante para a sua equipa pode estar entre as mais utilizadas na página. Pode ser um ótimo ponto de partida para melhorias de design para o seu site.

Se o seu evento inicial é um evento personalizado, a primeira coluna mostra quais usuários tinha apenas depois de executar essa ação. Tal como acontece com as vistas de página, considere se o comportamento observado dos seus utilizadores corresponde aos objetivos e as expectativas da sua equipa. Se o evento de inicial selecionado for "Adicionar Item ao carrinho de compras", por exemplo, observe se "Ir para Check-out" e "Adquirir concluída", que são apresentados na visualização em breve. Se o comportamento do utilizador é diferente do suas expectativas, utilizar a visualização para compreender a forma como os utilizadores estão a obter "interceptadas" por design atual do seu site.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Onde estão os locais que os utilizadores mais alterações a partir do seu site?

Procure **terminou sessão** nós que aparecem alto de segurança numa coluna na visualização, especialmente no início num fluxo. Isso significa que muitos usuários provavelmente formados a partir do seu site depois de seguir o caminho anterior de páginas e interações de interface do Usuário. Por vezes, volume de alterações esperado - depois de concluir a compra ser efetuada num site de comércio eletrónico, por exemplo – mas, normalmente, o volume de alterações é um sinal de problemas de design, fraco desempenho ou outros problemas com seu site que pode ser melhorada.

Tenha em mente, que **terminou sessão** nós baseiam-se apenas na telemetria recolhida por este recurso do Application Insights. Se o Application Insights não receber telemetria para certas interações de utilizador, os utilizadores foi ainda ter interagir com seu site dessas formas, depois da ferramenta fluxos de utilizador diz a sessão terminou.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Existem locais onde os utilizadores repetir a mesma ação repetidamente?

Procure uma vista de página ou eventos personalizados, que é repetido por vários utilizadores em passos subsequentes na visualização. Isso normalmente significa que os utilizadores estão a efetuar ações repetitivas em seu site. Se encontrar repetição, considere alterar o design do seu site ou adicionar novas funcionalidades para reduzir a repetição. Por exemplo, adicionar a funcionalidade de edição em volume se encontrar utilizadores efetuar ações repetitivas em cada linha de um elemento de tabela.

## <a name="common-questions"></a>Perguntas comuns

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Não representam o evento inicial na primeira vez que o evento é apresentado numa sessão, nem sempre aparece numa sessão?

O evento inicial na visualização representa apenas na primeira vez que um utilizador enviou essa visualização de página ou eventos personalizados durante uma sessão. Se os utilizadores podem enviar o evento inicial várias vezes numa sessão, em seguida, a coluna "Passo 1" apenas mostra como os utilizadores se comportam depois do *primeiro* instância de evento inicial, nem todas as instâncias.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Alguns de nós no meu visualização são demasiado alto nível. Por exemplo, um nó que vai dizer "Clicked do botão". Como posso decompô-la em nós mais detalhadas?

Utilize o **dividir por** opções na **editar** menu:

1. Escolha o evento que pretende dividir **evento** menu.
2. Escolha uma dimensão no **dimensão** menu. Por exemplo, se tiver um evento chamado "Botão clicado", experimente uma propriedade personalizada denominada "Nome do botão".

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral da utilização](usage-overview.md)
* [Users, Sessions, and Events](usage-segmentation.md) (Utilizadores, Sessões e Eventos)
* [Retenção](usage-retention.md)
* [Adicionar eventos personalizados à sua aplicação](../../azure-monitor/app/api-custom-events-metrics.md)