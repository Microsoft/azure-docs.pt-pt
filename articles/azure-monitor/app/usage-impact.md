---
title: Impacto de utilização de informações de aplicação do Azure | Documentos da Microsoft
description: Analisar como diferentes propriedades potencialmente influencia as taxas de conversão das partes das suas aplicações.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 8efab173f464b67c0c88c87ee28ea7fa19980501
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373221"
---
# <a name="impact-analysis-with-application-insights"></a>Análise de impacto com o Application Insights

Impacto analisa a forma como os tempos de carregamento e outras propriedades influenciam a taxas de conversão em várias partes da sua aplicação. Para colocar mais precisamente, Deteta como **qualquer dimensão** de um **vista de página**, **evento personalizado**, ou **pedido** afeta a utilização de um diferentes **vista de página** ou **evento personalizado**. 

![Ferramenta de impacto](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Ainda não sabe o que impacto faz?

Uma forma de pensar em impacto é como a ferramenta ultimate para liquidar argumentos com alguém da Equipe sobre como lentidão em algum aspecto do seu site está a afetar se os utilizadores continuem. Embora os usuários podem tolerar uma determinada quantidade de lentidão, o impacto dá-lhe informações sobre qual a melhor forma de equilibrar a Otimização e desempenho para maximizar a conversão do utilizador.

Mas analisar o desempenho é apenas um subconjunto de capacidades do impacto. Uma vez que o impacto suporta dimensões e eventos personalizados, respondendo a perguntas como como escolha de navegador do usuário correlacionar com diferentes taxas de conversão são apenas alguns cliques.

![Conversão de captura de ecrã por navegadores](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Recurso do Application Insights pode conter vistas de página ou eventos personalizados para usar a ferramenta de impacto. [Saiba como configurar a sua aplicação para recolher as vistas de página automaticamente com o Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md). Também tenha em atenção que o tamanho de exemplo, uma vez que está analisando correlação, é importante.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>É o tempo de carregamento de página afetar quantas pessoas converter em minha página?

Para começar a responder a perguntas com a ferramenta de impacto, selecione uma vista de página inicial, evento personalizado ou pedido.

![Ferramenta de impacto](./media/usage-impact/0002-dropdown.png)

1. Selecione uma vista de página a partir da **para a vista de página** lista pendente.
2. Deixe o **analisar como seu** lista suspensa a opção predefinida **duração** (neste contexto **duração** é um alias para **o tempo de carregamento de página**.)
3. Para o **afeta a utilização de** lista pendente, selecione um evento personalizado. Este evento deve corresponder a um elemento de interface do Usuário na vista de página que selecionou no passo 1.

![Captura de ecrã dos resultados](./media/usage-impact/0003-results.png)

Neste caso, como **página do produto** tempo de carregamento aumenta a taxa de conversão para **produto de compra clicado** ficar inativo. Com base na distribuição acima, uma duração de carregamento de página ideal de 3,5 segundos poderia ser direcionada para alcançar uma taxa de conversão de 55% potenciais. Melhorias de desempenho adicionais para reduzir o tempo de carregamento abaixo segundos 3,5 atualmente está correlacionado com benefícios adicionais de conversão.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>E se estou de controlo vistas de página ou tempos de carga de formas personalizadas?

Impacto suporta propriedades padrão e personalizadas e medidas. Utilize o que quiser. Em vez de período de tempo, utilize filtros nos eventos primários e secundários para obter mais específicas.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Os utilizadores de diferentes países ou regiões converter a taxas diferentes?

1. Selecione uma vista de página a partir da **para a vista de página** lista pendente.
2. Escolha "País ou região" na **analisar como seu** pendente
3. Para o **afeta a utilização de** lista pendente, selecione um evento personalizado que corresponde a um elemento de interface do Usuário na vista de página selecionou no passo 1.

Neste caso, os resultados já não se encaixa num modelo de eixo x contínuo que tinham no primeiro exemplo. Em vez disso, é apresentada uma visualização semelhante a um funil segmentado. Ordenar por **utilização** para ver a variação de conversão para o evento personalizado com base no país.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Como a ferramenta de impacto calcular estas taxas de conversão?

Nos bastidores, a ferramenta de impacto depende da [coeficiente de correlação Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Os resultados são calculados entre -1 e 1 por -1, representando uma correlação linear negativa e 1 que representa uma correlação linear positiva.

A estrutura básica de como funciona a análise de impacto é o seguinte:

Permitem _A_ = o página principal vista/eventos/pedido personalizado que selecionar na primeira lista pendente. (**Para a vista de página**).

Permitem _B_ = o evento de exibição/personalizado de página secundária selecionar (**afeta a utilização de**).

Impacto analisa um exemplo de todas as sessões dos utilizadores no intervalo de tempo selecionado. Para cada sessão, ele procura por cada ocorrência dos _A_.

Sessões, em seguida, são divididos em dois tipos diferentes de _subsessions_ com base em uma das duas condições:

- Um subsession convertido consiste numa sessão que termina com um _B_ eventos e abrange tudo _um_ eventos que ocorrem antes _B_.
- Um não convertido subsession ocorre quando todos os _uma_do ocorrer sem um terminal _B_.

Como, por fim, é calculado o impacto varia consoante se que estamos a analisar por métrica ou por dimensão. Para todos os de métricas _A_num subsession são transformadas em médias. Enquanto que para dimensões o valor de cada _uma_ contribui _1/N_ para o valor atribuído a _B_ onde _N_ é o número de _A_no subsession.

## <a name="next-steps"></a>Passos Seguintes

- Para permitir experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já tiver enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de utilizador](usage-send-user-context.md)