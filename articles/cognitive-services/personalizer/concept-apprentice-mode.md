---
title: Modo aprendiz - Personalizer
description: ''
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 2697ab4b32edbd4841f2b11725fda46e90e7ae7e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599405"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Utilize o modo Aprendiz para treinar o Personalizer sem afetar a sua aplicação existente

Devido à natureza da Aprendizagem de Reforço no **mundo real,** um modelo Personalizer só pode ser treinado num ambiente de produção. Ao implementar um novo caso de utilização, o modelo Personalizer não está a funcionar de forma eficiente porque leva tempo para que o modelo seja suficientemente treinado.  **O modo aprendiz** é um comportamento de aprendizagem que facilita esta situação e permite-lhe ganhar confiança no modelo – sem que o desenvolvedor mude qualquer código.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>O que é modo Aprendiz?

Semelhante à forma como um aprendiz aprende com um mestre, e com experiência pode melhorar; O modo aprendiz é um _comportamento_ que permite ao Personalizer aprender observando os resultados obtidos a partir da lógica de aplicação existente.

O personalizer treina imitando a mesma saída que a aplicação. À medida que mais eventos fluem, o Personalizer pode _alcançar_ a aplicação existente sem afetar a lógica e os resultados existentes. As métricas, disponíveis no portal Azure e na API, ajudam-no a compreender o desempenho à medida que o modelo aprende.

Uma vez que o Personalizer tenha aprendido e alcançado um certo nível de compreensão, o desenvolvedor pode mudar o comportamento do modo Aprendiz para o modo Online. Nessa altura, o Personalizer começa a influenciar as ações na API rank.

## <a name="purpose-of-apprentice-mode"></a>Propósito do Modo Aprendiz

O modo aprendiz dá-lhe confiança no serviço Personalizer e nas suas capacidades de machine learning, e garante que o serviço é enviado informação que pode ser aprendida – sem correr o risco de tráfego online.

As duas principais razões para utilizar o modo Aprendiz são:

* Mitigando o **Frio Começa**: O modo aprendiz ajuda a gerir e avaliar o custo do tempo de aprendizagem de um modelo "novo" - quando não está a devolver a melhor ação e não alcançou um nível de eficácia satisfatório de cerca de 75-85%.
* **Características**de Ação e Contexto de Validação : Características enviadas em ações e contexto podem ser inadequadas ou imprecisas - muito pouco, demasiado, incorreto ou demasiado específico para treinar o Personalizer para atingir a taxa de eficácia ideal. Utilize avaliações de [funcionalidades](concept-feature-evaluation.md) para encontrar e corrigir problemas com funcionalidades.

## <a name="when-should-you-use-apprentice-mode"></a>Quando deve usar o modo Aprendiz?

Utilize o modo Apprentice para treinar o Personalizer para melhorar a sua eficácia através dos seguintes cenários, deixando a experiência dos seus utilizadores não afetada pelo Personalizer:

* Está a implementar o Personalizer num novo caso de utilização.
* Alterou significativamente as funcionalidades que envia em Contexto ou Ações.
* Mudaste significativamente quando e como calculas as recompensas.

O modo aprendiz não é uma forma eficaz de medir o impacto que o Personalizer está a ter nas pontuações de recompensa. Para medir a eficácia do Personalizer na escolha da melhor ação possível para cada chamada de Rank, utilize [avaliações offline](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Quem deve usar o modo Aprendiz?

O modo aprendiz é útil para desenvolvedores, cientistas de dados e decisores empresariais:

* **Os desenvolvedores** podem usar o modo Apprentice para se certificarem de que as APIs de Rank e Reward estão a ser utilizadas corretamente na aplicação, e que as funcionalidades que estão a ser enviadas para personalizar a partir da aplicação não contém bugs ou funcionalidades não relevantes, como um carimbo de tempo ou um elemento UserID.

* **Os cientistas** de dados podem usar o modo Aprendiz para validar que as funcionalidades são eficazes para treinar os modelos Personalizer, que os tempos de espera de recompensa não são muito longos ou curtos.

* **Os Business Decision Makers** podem usar o modo Apprentice para avaliar o potencial do Personalizer para melhorar os resultados (ou seja, recompensas) em comparação com a lógica de negócio existente. Isto permite-lhes tomar uma decisão informada com impacto na experiência do utilizador, onde estão em jogo receitas reais e satisfação do utilizador.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Comparar Comportamentos - Modo aprendiz e modo online

Aprender quando no modo Aprendiz difere do modo Online das seguintes formas.

|Área|Modo aprendiz|Modo online|
|--|--|--|
|Impacto na experiência do utilizador|Pode utilizar o comportamento do utilizador existente para treinar o Personalizer, deixando-o observar (não afetar) o que teria sido a sua **ação predefinida** e a recompensa que obteve. Isto significa que a experiência dos seus utilizadores e os resultados do negócio deles não serão impactados.|Exibir a ação de topo devolvida da chamada rank para afetar o comportamento do utilizador.|
|Velocidade de aprendizagem|O personalizer aprenderá mais lentamente quando estiver no modo Aprendiz do que quando aprender no modo Online. O modo aprendiz só pode aprender observando as recompensas obtidas pela sua **ação padrão**, que limita a velocidade de aprendizagem, uma vez que nenhuma exploração pode ser realizada.|Aprende mais rápido porque pode explorar o modelo atual e explorar novas tendências.|
|Eficácia de aprendizagem "Teto"|O personalizer pode aproximar-se, muito raramente corresponder, e nunca exceder o desempenho da sua lógica de negócio base (o total de recompensa alcançado pela **ação padrão** de cada chamada rank).|O personalizer deve exceder a linha de base das aplicações, e com o tempo em que está estagnado deve realizar avaliação offline e avaliação de recursos para continuar a obter melhorias no modelo. |
|Valor api de classificação para recompensaActionId|A experiência dos utilizadores não é impactada, já que _rewardActionId_ é sempre a primeira ação que envia no pedido de Rank. Por outras palavras, o Rank API não faz nada visível para a sua aplicação durante o modo Aprendiz. A Recompensa APIs na sua aplicação não deve alterar a forma como utiliza a API reward entre um modo e outro.|A experiência dos utilizadores será alterada pela _recompensaActionId_ que o Personalizer escolhe para a sua aplicação. |
|Avaliações|O Personalizer mantém uma comparação dos totais de recompensa que a sua lógica de negócio padrão está recebendo, e os totais de recompensa personaliza que o Personalizer estaria recebendo se em modo Online nessa altura. Uma comparação está disponível no portal Azure para esse recurso|Avalie a eficácia do Personalizer executando [avaliações offline](concepts-offline-evaluation.md), que permitem comparar as recompensas totais que a Personalizer obteve com as potenciais recompensas da linha de base da aplicação.|

Uma nota sobre a eficácia do modo aprendiz:

* A eficácia do Personalizer no modo Aprendiz raramente atingirá perto de 100% da linha de base da aplicação; e nunca ultrapassá-lo.
* As melhores práticas seriam não tentar chegar a 100% de realização; mas um intervalo de 75 a 85% deve ser direcionado em função do caso de utilização.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Usando o modo Aprendiz para treinar com dados históricos

Se tiver uma quantidade significativa de dados históricos, gostaria de usar para treinar o Personalizer, pode utilizar o modo Apprentice para reproduzir os dados através do Personalizer.

Crie o Personalizer em Modo Aprendiz e crie um script que chame Rank com as ações e características de contexto a partir dos dados históricos. Ligue para a API reward com base nos seus cálculos dos registos destes dados. Você precisará de aproximadamente 50.000 eventos históricos para ver alguns resultados, mas 500.000 é recomendado para uma maior confiança nos resultados.

Ao treinar a partir de dados históricos, recomenda-se que os dados enviados (funcionalidades de contexto e ações, o seu layout no JSON utilizado para pedidos de Rank, e o cálculo da recompensa neste conjunto de dados de formação), correspondam aos dados (funcionalidades e cálculo da recompensa) disponíveis a partir da aplicação existente.

Os dados offline e pós-facto tendem a ser mais incompletos e mais barulhentos e diferem em formato. Embora a formação a partir de dados históricos seja possível, os resultados de fazê-lo podem ser inconclusivos e não um bom previsão de quão bem o Personalizer irá aprender, especialmente se as funcionalidades variam entre dados anteriores e a aplicação existente.

Tipicamente para personalizer, quando comparado com a formação com dados históricos, mudar de comportamento para o modo Aprendiz e aprender com uma aplicação existente é um caminho mais eficaz para ter um modelo eficaz, com menos trabalho, engenharia de dados e trabalho de limpeza.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Utilização do modo aprendiz versus testes A/B

Só é útil fazer testes De/B de tratamentos Personalizer uma vez que tenha sido validado e esteja a aprender no modo Online. No modo Aprendiz, apenas a **ação predefinida** é utilizada, o que significa que todos os utilizadores veriam efetivamente a experiência de controlo.

Mesmo que o Personalizer seja apenas o _tratamento,_ o mesmo desafio está presente na validação dos dados é bom para treinar o Personalizer. Em vez disso, o modo aprendiz poderia ser utilizado, com 100% do tráfego, e com todos os utilizadores a obterem a experiência de controlo (não afetada).

Uma vez que você tem um caso de uso usando Personalizer e aprendizagem on-line, experiências A/B permitem-lhe fazer coortes controladas e comparação científica de resultados que podem ser mais complexos do que os sinais usados para recompensas. Uma pergunta de exemplo que um teste A/B poderia responder é:`In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Passos seguintes

* Conheça [eventos ativos e inativos](concept-active-inactive-events.md)