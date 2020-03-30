---
title: Impacto de utilização de insights de aplicação Azure Microsoft docs
description: Analise como diferentes propriedades podem ter impacto nas taxas de conversão para partes das suas apps.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671040"
---
# <a name="impact-analysis-with-application-insights"></a>Análise de impacto com insights de aplicação

Impact analisa como os tempos de carga e outras propriedades influenciam as taxas de conversão para várias partes da sua aplicação. Para ser mais precisamente, descobre como **qualquer dimensão** de uma visão de **página**, **evento personalizado,** ou **pedido** afeta o uso de uma visão de **página** diferente ou **evento personalizado**. 

![Ferramenta de impacto](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Ainda não sabe o que o Impacto faz?

Uma maneira de pensar no Impacto é como a ferramenta final para resolver argumentos com alguém da sua equipa sobre como a lentidão em algum aspeto do seu site está a afetar se os utilizadores ficam por perto. Embora os utilizadores possam tolerar uma certa lentidão, o Impacto dá-lhe uma visão da melhor forma de equilibrar a otimização e o desempenho para maximizar a conversão do utilizador.

Mas analisar o desempenho é apenas um subconjunto das capacidades do Impact. Uma vez que o Impact suporta eventos e dimensões personalizadas, responder a questões como como é que a escolha do navegador do utilizador está relacionada com diferentes taxas de conversão estão a poucos cliques de distância.

![Conversão de screenshot por navegadores](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> O seu recurso Application Insights deve conter visualizações de página ou eventos personalizados para utilizar a ferramenta Impacto. [Saiba como configurar a sua aplicação para recolher automaticamente as visualizações da página com os Insights de Aplicação JavaScript SDK](../../azure-monitor/app/javascript.md). Tenha também em mente que, uma vez que está a analisar a correlação, o tamanho da amostra importa.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>O tempo de carregamento da página está a afetar quantas pessoas se convertem na minha página?

Para começar a responder a perguntas com a ferramenta Impacto, escolha uma visão inicial da página, evento personalizado ou pedido.

![Ferramenta de impacto](./media/usage-impact/0002-dropdown.png)

1. Selecione uma vista de página a partir **da página Para a página** dropdown.
2. Deixe a **análise de como** a sua queda na seleção padrão de **Duração** (neste contexto **A duração** é um pseudónimo para o Tempo de Carga da **Página**.)
3. Para os **impactos do uso do** dropdown, selecione um evento personalizado. Este evento deve corresponder a um elemento UI na visualização da página selecionada no passo 1.

![Screenshot dos resultados](./media/usage-impact/0003-results.png)

Neste caso, à medida que o tempo de carga da Página do **Produto** aumenta a taxa de conversão para comprar **produto clicado** diminui. Com base na distribuição acima, uma duração ideal de carga de página de 3,5 segundos poderia ser direcionada para alcançar uma taxa de conversão potencial de 55%. Outras melhorias de desempenho para reduzir o tempo de carga abaixo dos 3,5 segundos não se relacionam atualmente com benefícios adicionais de conversão.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>E se eu estiver a seguir as vistas das páginas ou os tempos de carregamento de formapersonalizada?

O impacto suporta propriedades e medições padrão e personalizadas. Use o que quiser. Em vez de duração, utilize filtros nos eventos primários e secundários para obter mais detalhes.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Os utilizadores de diferentes países ou regiões convertem-se a taxas diferentes?

1. Selecione uma vista de página a partir **da página Para a página** dropdown.
2. Escolha "País ou região" em **análise de como** a sua queda
3. Para os **impactos do uso do** dropdown, selecione um evento personalizado que corresponda a um elemento UI na vista da página que escolheu no passo 1.

Neste caso, os resultados já não se enquadram num modelo de eixo X contínuo, como fizeram no primeiro exemplo. Em vez disso, é apresentada uma visualização semelhante a um funil segmentado. Ordenar por **Utilização** para ver a variação da conversão para o seu evento personalizado com base em país/região.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Como é que a ferramenta Impact calcula estas taxas de conversão?

Sob o exaustor, a ferramenta Impact baseia-se no coeficiente de [correlação Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Os resultados são calculados entre -1 e 1 com -1 representando uma correlação linear negativa e 1 representando uma correlação linear positiva.

A desagregação básica de como funciona a Análise de Impacto é a seguinte:

Deixe _A_ = a visualização/evento/pedido personalizado da página principal que selecionar na primeira queda. (Para**a vista da página).**

Deixe _B_ = a visão/evento personalizado da página secundária que selecionar **(impacta a utilização de**).

O impacto analisa uma amostra de todas as sessões dos utilizadores na faixa hortema selecionada. Para cada sessão, procura cada ocorrência de _A_.

As sessões são então divididas em dois tipos diferentes de _subsessões_ com base numa de duas condições:

- Uma subsessão convertida consiste numa sessão que termina com um evento _B_ e engloba todos os eventos _A_ que ocorrem antes _de B_.
- Uma subsessão não convertida ocorre quando todos _os A_'s ocorrem sem um terminal _B_.

Como o Impacto é calculado, em última análise varia com base em estarmos a analisar por métrica ou por dimensão. Para as métricas todos _os A's_em uma subsessão são médias. Considerando que, para as dimensões, o valor de cada _A_ contribui _1/N_ para o valor atribuído a _B_ onde _n_ é o número de _A's_na subsessão.

## <a name="next-steps"></a>Passos seguintes

- Para permitir experiências de utilização, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [visualizações de páginas.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Se já envia eventos personalizados ou visualizações de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)
