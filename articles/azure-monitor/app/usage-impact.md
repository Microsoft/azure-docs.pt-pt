---
title: Aplicativo Azure o impacto de uso de informações | Microsoft docs
description: Analise como propriedades diferentes podem impactar as taxas de conversão para partes de seus aplicativos.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fffe71cb80be7795201ab672ca632788f4f18e5c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899455"
---
# <a name="impact-analysis-with-application-insights"></a>Análise de impacto com Application Insights

O impacto analisa como os tempos de carregamento e outras propriedades influenciam as taxas de conversão para várias partes do seu aplicativo. Para colocá-lo mais precisamente, ele descobre como **qualquer dimensão** de uma **exibição de página**, **evento personalizado**ou **solicitação** afeta o uso de uma exibição de **página** diferente ou de um **evento personalizado**. 

![Ferramenta de impacto](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Ainda não tem certeza de qual impacto?

Uma maneira de pensar no impacto é como a ferramenta definitiva para a liquidação de argumentos com alguém em sua equipe sobre como a lentidão em algum aspecto do seu site está afetando se os usuários estão em um lugar. Embora os usuários possam tolerar uma certa quantidade de lentidão, o impacto lhe dá informações sobre como balancear melhor a otimização e o desempenho para maximizar a conversão do usuário.

Mas analisar o desempenho é apenas um subconjunto de recursos de impacto. Como o impacto dá suporte a dimensões e eventos personalizados, responder a perguntas como como a escolha do navegador do usuário se correlaciona com diferentes taxas de conversão tem apenas alguns cliques.

![Conversão de captura de tela por navegadores](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> O recurso de Application Insights deve conter exibições de página ou eventos personalizados para usar a ferramenta de impacto. [Saiba como configurar seu aplicativo para coletar exibições de página automaticamente com o SDK Application insights JavaScript](../../azure-monitor/app/javascript.md). Também tenha em mente que, como você está analisando a correlação, o tamanho da amostra é importante.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>O tempo de carregamento de página está afetando quantas pessoas convertem na minha página?

Para começar a responder perguntas com a ferramenta de impacto, escolha uma exibição de página inicial, evento personalizado ou solicitação.

![Ferramenta de impacto](./media/usage-impact/0002-dropdown.png)

1. Selecione um modo de exibição de página no menu suspenso da **exibição de página** .
2. Deixe a opção **analisar como sua** lista suspensa na seleção padrão de **duração** (nessa **duração** de contexto é um alias para o **tempo de carregamento da página**.)
3. Para o **impacta o uso de** DropDown, selecione um evento personalizado. Esse evento deve corresponder a um elemento de interface do usuário no modo de exibição de página que você selecionou na etapa 1.

![Captura de tela de resultados](./media/usage-impact/0003-results.png)

Nessa instância, à medida que o tempo de carregamento **da página do produto** aumenta, a taxa de conversão para **comprar o produto** é inativa. Com base na distribuição acima, uma duração de carregamento de página ideal de 3,5 segundos pode ser destinada a atingir uma taxa de conversão de 55% potencial. Aprimoramentos de desempenho adicionais para reduzir o tempo de carregamento abaixo de 3,5 segundos não correlacionem-se atualmente a outros benefícios de conversão.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>E se eu estiver rastreando exibições de página ou tempos de carregamento de maneiras personalizadas?

O impacto dá suporte a propriedades e medidas padrão e personalizadas. Use o que desejar. Em vez de duração, use filtros nos eventos primário e secundário para obter mais informações específicas.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Os usuários de diferentes países ou regiões convertem em taxas diferentes?

1. Selecione um modo de exibição de página no menu suspenso da **exibição de página** .
2. Escolha "país ou região" em **analisar como seu** menu suspenso
3. Para o **impacta o uso de** DropDown, selecione um evento personalizado que corresponda a um elemento de interface do usuário no modo de exibição de página escolhido na etapa 1.

Nesse caso, os resultados não se encaixam mais em um modelo de eixo x contínuo como faziam no primeiro exemplo. Em vez disso, uma visualização semelhante a um funil segmentado é apresentada. Classifique por **uso** para exibir a variação de conversão em seu evento personalizado com base no país/região.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Como a ferramenta de impacto calcula essas taxas de conversão?

Nos bastidores, a ferramenta de impacto depende do [coeficiente de correlação Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Os resultados são computados entre-1 e 1 com-1 representando uma correlação linear negativa e 1 representando uma correlação linear positiva.

A divisão básica de como a análise de impacto funciona é a seguinte:

Let _a = a exibição_ de página principal/evento/solicitação personalizada que você selecionou na primeira lista suspensa. (**Para a exibição de página**).

Deixe _B_ = a exibição de página secundária/evento personalizado que você selecionar (**afeta o uso de**).

O impacto examina uma amostra de todas as sessões de usuários no intervalo de tempo selecionado. Para cada sessão, ele procura cada ocorrência de _A_.

Em seguida, as sessões são divididas em dois tipos diferentes de _subsessão_ com base em uma das duas condições:

- Uma subsessão convertida consiste em uma sessão que termina com um evento _B_ e _abrange todos os_ eventos que ocorrem antes de _B_.
- Uma subsessão não convertida ocorre quando _o_problema ocorre sem um terminal _B_.

Como o impacto é calculado por fim varia de acordo com a análise de métricas ou por dimensão. Para métricas, todas as _a_em uma subsessão são calculadas por média. Ao passo que, para dimensões, o valor de cada _um_ contribui com _1/N_ para o valor atribuído a _B_ , em que _N_ é o número de _um_na subsessão.

## <a name="next-steps"></a>Passos seguintes

- Para habilitar as experiências de uso, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já enviar eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários usam seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos do Utilizador](usage-flows.md)
    - [Livros](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de usuário](usage-send-user-context.md)