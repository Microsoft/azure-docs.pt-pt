---
title: Dashboard - compreensão de idiomas
titleSuffix: Azure Cognitive Services
description: Corrigi intenções com o dashboard de resumo de análise, uma ferramenta de geração de relatórios visualizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a518a697369ff74689a0c4ac05af96453b6a5ca4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072546"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como utilizar o Dashboard para melhorar a sua aplicação

Encontrar e corrigir problemas com os objetivos de seu aplicativo preparado quando estiver a utilizar expressões de exemplo. O dashboard de resumo apresenta informações da aplicação global, com os destaques da objetivos que devem ser corrigidos. 

Reveja o Dashboard de análise é um processo iterativo, repetido à medida que altera e melhorar o seu modelo.

Esta página não terão análise relevante para as aplicações que não têm quaisquer expressões de exemplo no intenções, conhecidas como _só de padrão_ aplicações. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quais problemas podem ser corrigida a partir do dashboard?

Três problemas resolvidos no dashboard são:

|Problema|Cor do gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isto ocorre quando a quantidade de expressões de exemplo varia significativamente. Tem de ter todas as intenções _aproximadamente_ o mesmo número de expressões de exemplo -, exceto a intenção None. Ele deverá ter apenas 10% - 15% da quantidade total de expressões com na aplicação.<br><br> Se os dados são desequilibrados, mas a precisão de intenção for superior a determinado limiar, esse desequilíbrio não é reportado como um problema.<br><br>**Iniciar com este problema - poderá ser a causa raiz dos outros problemas.**|
|Predições claras|Orange|Isto ocorre quando a intenção superior e as pontuações da intenção seguinte estão próximas o suficiente que eles podem inverte na formação seguinte, devido a [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais expressões de exemplo adicionados à intenção. |
|Previsões incorretas|Vermelho|Isto ocorre quando uma expressão de exemplo não está previsto para a intenção etiquetada (a intenção é no).|

Previsões corretas são representados com a azul de cor.

O dashboard de resumo mostra esses problemas e indica os objetivos são afetados e sugere que deve fazer para melhorar a aplicação. 

## <a name="before-app-is-trained"></a>Antes do aplicativo está preparado 

Antes de preparar a aplicação, o dashboard de resumo não contém qualquer sugestão para correções. Treine a sua aplicação para ver estas sugestões.  

## <a name="check-your-publishing-status"></a>Verificar o estado da publicação

O **estado de publicação** cartão contém informações sobre o Active Directory versão da última publicação. 

Verifique se a versão do Active Directory é a versão que pretende corrigir. 

![Os serviços externos da aplicação do dashboard de resumo mostra, publicados regiões e agregados de cliques de ponto final.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isto também mostra a quaisquer serviços externos, regiões publicados e agregados de cliques de ponto final. 

## <a name="review-training-evaluation"></a>Avaliação de treinamento de revisão

O **avaliação de treinamento** cartão contém o resumo agregado de precisão geral da sua aplicação por área. A classificação indica intenção qualidade. 

![O cartão de avaliação de treinamento contém a primeira área de informações sobre a precisão geral da sua aplicação.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica os objetivos previstos corretamente e as áreas de problema com diferentes cores. Como melhorar a aplicação com as sugestões, o que aumenta pontuação. 

As correções sugeridas são separadas por tipo de problema e são os mais significativos para a sua aplicação. Se desejar rever e corrigir problemas por intenção, utilize o **[intenções com erros](#intents-with-errors)** cartão na parte inferior da página. 

Cada área de problema tem objetivos que precisam ser corrigidos. Ao selecionar o nome da intenção, o **intenção** é aberta a página com um filtro aplicado para as expressões. Este filtro permite-lhe concentrar-se em expressões de com que estão a causar o problema.

### <a name="compare-changes-across-versions"></a>Comparar alterações em versões

Crie uma nova versão antes de efetuar alterações à aplicação. Na nova versão, fazer as alterações sugeridas em expressões de exemplo a intenção, em seguida, dar formação novamente. Na página do Dashboard **avaliação de formação** cartão, utilize o **Show de alteração de versão treinado** para comparar as alterações. 

![Comparar alterações em versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigir versão ao adicionar ou editar expressões de exemplo e reparametrização

O método primário de corrigir a sua aplicação será adicionar ou editar expressões de exemplo e voltar a preparar. As expressões de novas ou alteradas tem de seguir as diretrizes para [diversificados expressões com](luis-concept-utterance.md).

Adicionar expressões de exemplo deve ser feita por alguém que:

* tem um alto nível de compreensão de quais são expressões com dos objetivos diferentes
* sabe como expressões em intenção, um podem ser confundidos com outro objetivo
* Pode decidir se dois objetivos, que freqüentemente são confundidos uns com os outros, devem ser recolhidos num único objetivo e os dados de diferentes puxado com entidades

### <a name="patterns-and-phrase-lists"></a>Padrões e as listas de frase

A página de análise não indica quando usar [padrões](luis-concept-patterns.md) ou [frase listas](luis-concept-feature.md). Se adicioná-los, ele pode ajudar com predições cifradas ou incorretas, mas não ajudará com desequilíbrio de dados. 

### <a name="review-data-imbalance"></a>Desequilíbrio de dados de revisão

Iniciar com este problema - poderá ser a causa raiz dos outros problemas.

O **desequilíbrio dados** lista intenção mostra objetivos que precisam de mais expressões para corrigir o desequilíbrio de dados. 

**Para corrigir este problema**:

* Adicionar expressões mais à intenção, em seguida, dar formação novamente. 

Não adicione expressões com a intenção None, a menos que o que é sugerido no dashboard de resumo.

> [!Tip]
> Utilizar a terceira seção na página, **expressões com por intenção** com o **expressões com (número)** definir, como um guia visual rápido tem de expressões com mais do que intenções.  
    ![Use "Expressões com (número)" para encontrar intenções com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Reveja as previsões incorretas

O **previsão incorreta** lista intenção mostra objetivos que tenham expressões de com, que são utilizados como exemplos para um objetivo específico, mas estão previstos para objetivos diferentes. 

**Para corrigir este problema**:

* Edite expressões com sejam mais específicas a intenção e train novamente.
* Combine intenções se expressões com muito perto estão alinhados e prepare-se novamente.

### <a name="review-unclear-predictions"></a>Predições claras de revisão

O **predição clara** lista intenção mostra objetivos com expressões com pontuações de predição que não é o suficiente forma do seu rival mais próximo, o que a intenção de superior para a expressão pode ser alterado de preparação seguinte, devido a [ amostragem negativa](luis-how-to-train.md#train-with-all-data).

**Para corrigir este problema**;

* Edite expressões com sejam mais específicas a intenção e train novamente.
* Combine intenções se expressões com muito perto estão alinhados e prepare-se novamente.

## <a name="utterances-per-intent"></a>Expressões com por intenção

Este cartão mostra o estado de funcionamento geral do aplicativo em dos objetivos. Como corrigir intenções e voltar a preparar, continue a olhando este cartão para problemas.

O gráfico seguinte mostra um aplicativo bem equilibrado quase sem problemas para corrigir.

![O gráfico seguinte mostra um aplicativo bem equilibrado quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico seguinte mostra uma aplicação mal balanceada com muitos problemas para corrigir.

![O gráfico seguinte mostra um aplicativo bem equilibrado quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Coloque o cursor sobre a barra de cada intenção para obter informações sobre a intenção. 

![O gráfico seguinte mostra um aplicativo bem equilibrado quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilize o **ordenar por** funcionalidade para organizar as intenções por tipo de problema para que se possa focar dos objetivos mais problemáticos com esse problema. 

## <a name="intents-with-errors"></a>Objetivos com erros

Este cartão permite-lhe rever problemas para um objetivo específico. A vista predefinida deste cartão é dos objetivos mais problemáticos para que sabe onde concentrar seus esforços.

![Os objetivos com cartão de erros permite-lhe rever problemas para um objetivo específico. O cartão é filtrado para dos objetivos mais problemáticos, por predefinição, para que sabe onde concentrar seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico de anel superior mostra os problemas com a intenção entre os tipos de três problema. Se existirem problemas nos tipos de problema de três, cada tipo tem seu próprio gráfico abaixo, juntamente com quaisquer objetivos rival. 

### <a name="filter-intents-by-issue-and-percentage"></a>Objetivos de filtro por problema e a percentagem

Esta secção do cartão permite que encontre expressões de exemplo que estão falhando fora do limiar de erro. O ideal é que deseja previsões corretas para ser significativo. Esse percentual está empresas e dos clientes controlado por. 

Determine as porcentagens de limiar que se sente confortável para a sua empresa. 

O filtro permite que encontre intenções problema específico:

|Filtro|Percentagem sugerida|Objetivo|
|--|--|--|
|Objetivos mais problemáticos|-|**Comece por aqui** -corrigir as expressões em intenção, este irá melhorar a aplicação mais do que outras correções.|
|Previsões corretas abaixo|60%|Esta é a percentagem de expressões com a intenção selecionada que estão corretos, mas têm uma pontuação de confiança abaixo do limiar. |
|Predições claras acima|15%|Esta é a percentagem de expressões com a intenção selecionada que são confundidos com a intenção de rival mais próxima.|
|Previsões incorretas acima|15%|Esta é a percentagem de expressões com a intenção selecionada que estão a ser previsto incorretamente. |

### <a name="correct-prediction-threshold"></a>Limiar de previsão correta

O que é uma pontuação de confiança de predição de confiança para? No início do desenvolvimento de aplicativos, 60% pode ser o destino. Utilize o **corrigir previsões abaixo** com a percentagem de 60% para encontrar qualquer expressões na intenção selecionada que precisam ser corrigidos.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limiar de predição clara ou incorretas

Esses dois filtros permitem-lhe encontrar expressões com na intenção selecionada acima do limiar. Pode pensar essas porcentagens de dois como percentagens de erro. Se estiver familiarizado com uma taxa de erros de 10 a 15% para predições, defina o limiar de filtro para 15% para localizar todas as expressões acima este valor. 

## <a name="next-steps"></a>Passos Seguintes

* [Gerir recursos do Azure](luis-how-to-azure-subscription.md)
