---
title: Azure Application Insights Use Impact | Docs da Microsoft
description: Analise como diferentes propriedades podem ter impacto nas taxas de conversão para partes das suas apps.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51f938743d8da3c9a1dea546320662701e2b88c8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583523"
---
# <a name="impact-analysis-with-application-insights"></a>Análise de impacto com Insights de Aplicação

O impacto analisa como os tempos de carga e outras propriedades influenciam as taxas de conversão para várias partes da sua app. Para ser mais precisamente, descobre como **qualquer dimensão** de uma vista de **página,** **evento personalizado,** ou **pedido** afeta o uso de uma **visão** de página diferente ou **evento personalizado.** 

![Ferramenta de impacto](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Ainda não sabe o que o Impacto faz?

Uma maneira de pensar no Impacto é como a ferramenta final para resolver argumentos com alguém da sua equipa sobre como a lentidão em algum aspeto do seu site está a afetar se os utilizadores ficam por perto. Embora os utilizadores possam tolerar uma certa lentidão, o Impacto dá-lhe uma visão sobre a melhor forma de equilibrar a otimização e o desempenho para maximizar a conversão do utilizador.

Mas analisar o desempenho é apenas um subconjunto das capacidades do Impacto. Uma vez que o Impacto suporta eventos e dimensões personalizadas, responder a questões como como a escolha do navegador do utilizador correlaciona-se com diferentes taxas de conversão estão a poucos cliques de distância.

![Conversão de screenshot por navegadores](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> O seu recurso Application Insights deve conter visualizações de página ou eventos personalizados para utilizar a ferramenta Impact. [Saiba como configurar a sua aplicação para recolher as vistas da página automaticamente com o App Insights JavaScript SDK](./javascript.md). Tenha também em mente que, uma vez que está a analisar a correlação, o tamanho da amostra importa.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>O tempo de carga da página está a afetar quantas pessoas se convertem na minha página?

Para começar a responder a perguntas com a ferramenta Impact, escolha uma vista inicial de página, evento personalizado ou pedido.

![Screenshot que mostra onde escolher uma vista inicial da página, evento personalizado ou pedido.](./media/usage-impact/0002-dropdown.png)

1. Selecione uma vista de página a partir do dropdown da vista para **a página.**
2. Deixe a **análise de como** a sua queda na seleção padrão de **Duração** (Neste contexto **A duração** é um pseudónimo para **o tempo de carregamento da página**.)
3. Para os **impactos do uso do** dropdown, selecione um evento personalizado. Este evento deve corresponder a um elemento UI na vista de página selecionada no passo 1.

![Screenshot dos resultados](./media/usage-impact/0003-results.png)

Neste caso, à medida que o tempo de carga **da Página do Produto** aumenta a taxa de conversão para Produto de Compra **clicado** desce. Com base na distribuição acima, uma duração de carga de página ideal de 3,5 segundos poderia ser direcionada para alcançar uma taxa de conversão potencial de 55%. Atualmente, as melhorias de desempenho adicionais para reduzir o tempo de carga abaixo dos 3,5 segundos não estão atualmente relacionadas com benefícios adicionais de conversão.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>E se eu estiver a seguir as vistas da página ou os tempos de carga de maneiras personalizadas?

O impacto suporta propriedades e medições padrão e personalizadas. Use o que quiser. Em vez de duração, utilize filtros nos eventos primários e secundários para obter mais específicos.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Os utilizadores de diferentes países ou regiões convertem-se a taxas diferentes?

1. Selecione uma vista de página a partir do dropdown da vista para **a página.**
2. Escolha "País ou região" para **analisar como o seu** dropdown
3. Para os **impactos no uso do** dropdown, selecione um evento personalizado que corresponda a um elemento UI na vista de página que escolheu no passo 1.

Neste caso, os resultados já não se enquadram num modelo de eixo x contínuo, como fizeram no primeiro exemplo. Em vez disso, é apresentada uma visualização semelhante a um funil segmentado. Ordenar por **Utilização** para ver a variação da conversão para o seu evento personalizado com base em país/região.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Como calcula a ferramenta Impact estas taxas de conversão?

Sob o capot, a ferramenta Impact baseia-se no [coeficiente de correlação Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Os resultados são calculados entre -1 e 1 com -1 representando uma correlação linear negativa e 1 representando uma correlação linear positiva.

A desagregação básica de como funciona a Análise de Impacto é a seguinte:

Deixe _A_ = a vista/evento personalizado da página principal/pedido que selecione no primeiro dropdown. (**Para a vista da página).**

Deixe _B_ = a vista de página secundária/evento personalizado que seleciona **(impacta a utilização de**).

Impact olha para uma amostra de todas as sessões de utilizadores na faixa de tempo selecionada. Para cada sessão, procura cada ocorrência de _A_.

As sessões são então divididas em dois tipos diferentes de _subsesões_ com base numa de duas condições:

- Uma subsessão convertida consiste numa sessão que termina com um evento _B_ e engloba todos os eventos _A_ que ocorrem antes de _B_.
- Uma subsessão não convertida ocorre quando todas as _A's_ ocorrem sem um terminal _B_.

A forma como o Impacto é calculado varia em termos de base no facto de estarmos a analisar por métrica ou por dimensão. Para as métricas todos os _A's_ numa subsessão são mediados. Considerando que, para as dimensões, o valor de cada _A_ contribui em _1/N_ para o valor atribuído a _B_ onde _N_ é o número de _A's_ na subsessão.

## <a name="next-steps"></a>Passos seguintes

- Para ativar experiências de utilização, comece a enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) ou [vistas de página.](./api-custom-events-metrics.md#page-views)
- Se já envia eventos personalizados ou vistas de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos de Utilizador](usage-flows.md)
    - [Livros](../visualize/workbooks-overview.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)

