---
title: Modo aprendiz - Personalizer
description: Aprenda a usar o modo de aprendiz para ganhar confiança num modelo sem alterar qualquer código.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 531917d9c48915f71354b4cd35747ecd9d33a6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385035"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Utilize o modo Aprendiz para treinar o Personalizar sem afetar a sua aplicação existente

Devido à natureza do Reforço Aprendizagem do **Mundo Real,** um modelo personalizer só pode ser treinado num ambiente de produção. Ao implementar uma nova caixa de utilização, o modelo Personalizer não está a funcionar de forma eficiente porque leva tempo para que o modelo seja suficientemente treinado.  **O modo aprendiz** é um comportamento de aprendizagem que facilita esta situação e permite-lhe ganhar confiança no modelo – sem que o desenvolvedor altere qualquer código.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>O que é o modo Aprendiz?

Semelhante à forma como um aprendiz aprende com um mestre, e com experiência pode ficar melhor; O modo aprendiz é um _comportamento_ que permite ao Personaler aprender observando os resultados obtidos a partir da lógica de aplicação existente.

Os comboios personalizantes imitando a mesma saída que a aplicação. À medida que mais eventos _fluem,_ o Personalizer pode alcançar a aplicação existente sem afetar a lógica e os resultados existentes. As métricas, disponíveis no portal Azure e na API, ajudam-no a compreender o desempenho à medida que o modelo aprende.

Uma vez que o Personaler tenha aprendido e atingido um certo nível de compreensão, o desenvolvedor pode alterar o comportamento do modo Apprentice para o modo Online. Nessa altura, o Personalizer começa a influenciar as ações na API de classificação.

## <a name="purpose-of-apprentice-mode"></a>Fim do Modo aprendiz

O modo Aprendiz dá-lhe confiança no serviço Personalizar e nas suas capacidades de machine learning, e garante que o serviço é enviado informação que pode ser aprendida – sem arriscar o tráfego online.

As duas principais razões para utilizar o modo Aprendiz são:

* Mitigação **do Frio**: O modo aprendiz ajuda a gerir e avaliar o custo do tempo de aprendizagem de um modelo "novo" - quando não está a devolver a melhor ação e não alcançou um nível de eficácia satisfatório de cerca de 60-80%.
* **Validar funcionalidades de ação e contexto**: As funcionalidades enviadas em ações e contexto podem ser inadequadas ou imprecisas - muito pouco, demasiado, incorreta ou demasiado específica para treinar o Personalizador para atingir a taxa de eficácia ideal. Utilize [avaliações de funcionalidades](concept-feature-evaluation.md) para encontrar e corrigir problemas com funcionalidades.

## <a name="when-should-you-use-apprentice-mode"></a>Quando deve utilizar o modo Aprendiz?

Utilize o modo Apprentice para treinar o Personaler para melhorar a sua eficácia através dos seguintes cenários, deixando a experiência dos seus utilizadores inalterada pelo Personalizer:

* Está a implementar o Personaler num novo caso de utilização.
* Alterou significativamente as funcionalidades que envia no Contexto ou em Ações.
* Mudaste significativamente quando e como calculas as recompensas.

O modo aprendiz não é uma forma eficaz de medir o impacto que o Personalizer está a ter nas pontuações de recompensa. Para medir a eficácia do Personalização na escolha da melhor ação possível para cada chamada rank, utilize [avaliações offline](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Quem deve usar o modo Aprendiz?

O modo aprendiz é útil para desenvolvedores, cientistas de dados e decisores empresariais:

* **Os desenvolvedores** podem usar o modo Apprentice para garantir que as APIs rank e Reward estão a ser usadas corretamente na aplicação, e que as funcionalidades enviadas para Personaler a partir da aplicação não contêm bugs, ou funcionalidades não relevantes, como um sinal de tempo ou elemento UserID.

* **Os cientistas de dados** podem usar o modo Apprentice para validar que as funcionalidades são eficazes para treinar os modelos Personalizer, que os tempos de espera da recompensa não são muito longos ou curtos.

* **Os Decisores empresariais** podem usar o modo Aprendiz para avaliar o potencial do Personalizador para melhorar os resultados (isto é, recompensas) em comparação com a lógica de negócio existente. Isto permite-lhes tomar uma decisão informada com impacto na experiência do utilizador, onde estão em jogo receitas reais e satisfação do utilizador.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Comparar Comportamentos - Modo aprendiz e modo Online

Aprender quando em modo Aprendiz difere do modo Online das seguintes formas.

|Área|Modo Aprendiz|Modo online|
|--|--|--|
|Impacto na Experiência do Utilizador|Pode utilizar o comportamento do utilizador existente para treinar o Personalizer, deixando-o observar (não afetar) o que teria sido a sua **ação padrão** e a recompensa que obteve. Isto significa que a experiência dos seus utilizadores e os resultados do negócio deles não serão impactados.|Mostrar ação de topo devolvida da chamada Rank para afetar o comportamento do utilizador.|
|Velocidade de aprendizagem|O personalização aprenderá mais lentamente quando estiver em modo Aprendiz do que ao aprender em modo Online. O modo aprendiz só pode aprender observando as recompensas obtidas pela sua **ação padrão**, o que limita a velocidade de aprendizagem, uma vez que nenhuma exploração pode ser realizada.|Aprende mais rapidamente porque tanto pode explorar o modelo atual como explorar novas tendências.|
|Eficácia da aprendizagem "Teto"|O personalização pode aproximar-se, muito raramente, e nunca exceder o desempenho da sua lógica de negócio base (o total de recompensa alcançado pela **ação padrão** de cada chamada Rank). Este teto de aproximação é reduzido pela exploração. Por exemplo, com a exploração a 20% é muito improvável que o desempenho do modo de aprendiz exceda os 80%, e 60% é um alvo razoável para se formar em modo online.|O personalização deve exceder a linha de base das aplicações, e com o tempo em que esta estagna, deve realizar uma avaliação offline e uma avaliação de recursos para continuar a obter melhorias no modelo. |
|Valor API de classificação para recompensaActionId|A experiência dos utilizadores não é impactada, uma vez que _o RewardActionId_ é sempre a primeira ação que envia no pedido rank. Por outras palavras, a API rank não faz nada visível para a sua aplicação durante o modo Aprendiz. As APIs de recompensa na sua aplicação não devem alterar a forma como utiliza a API Reward entre um modo e outro.|A experiência dos utilizadores será alterada pela _recompensaActionId_ que o Personalizer escolhe para a sua aplicação. |
|Avaliações|O personalização mantém uma comparação dos totais de recompensa que a sua lógica de negócio padrão está a receber, e os totais de recompensa personalizer estariam recebendo se em modo Online nessa altura. Uma comparação está disponível no portal Azure para esse recurso|Avalie a eficácia do Personaler executando [avaliações offline,](concepts-offline-evaluation.md)que permitem comparar as recompensas totais que o Personalizer conseguiu face às potenciais recompensas da linha de base da aplicação.|

Uma nota sobre a eficácia do modo de aprendiz:

* A eficácia do personalização no modo Aprendiz raramente atingirá perto de 100% da linha de base da aplicação; e nunca ultrapassá-lo.
* As melhores práticas seriam não tentar atingir 100% de realização; mas um intervalo de 60% – 80% deve ser direcionado dependendo do caso de utilização.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Utilizar o modo Aprendiz para treinar com dados históricos

Se tiver uma quantidade significativa de dados históricos, gostaria de usar para treinar o Personalizer, pode utilizar o modo Apprentice para reproduzir os dados através do Personalizer.

Crie o Personalizador em Modo Aprendiz e crie um script que chame Rank com as ações e funcionalidades de contexto a partir dos dados históricos. Ligue para a API reward com base nos seus cálculos dos registos nestes dados. Você precisará de aproximadamente 50.000 eventos históricos para ver alguns resultados, mas 500.000 é recomendado para uma maior confiança nos resultados.

Ao treinar a partir de dados históricos, recomenda-se que os dados enviados (recursos para contexto e ações, o seu layout no JSON utilizado para pedidos de Rank, e o cálculo da recompensa neste conjunto de dados de formação), correspondam aos dados (características e cálculo da recompensa) disponíveis a partir da aplicação existente.

Os dados offline e pós-facto tendem a ser mais incompletos e ruidosos e diferem em formato. Embora a formação a partir de dados históricos seja possível, os resultados de fazê-lo podem ser inconclusivos e não um bom preditor de como o Personalizer vai aprender, especialmente se as características variam entre os dados anteriores e a aplicação existente.

Tipicamente para Personalizer, quando comparado com a formação com dados históricos, mudar o comportamento para o modo Aprendiz e aprender com uma aplicação existente é um caminho mais eficaz para ter um modelo eficaz, com menos trabalho, engenharia de dados e trabalho de limpeza.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Utilizando o modo aprendiz versus testes A/B

Só é útil fazer testes A/B de tratamentos Personalizer uma vez validado e está a aprender em modo Online. No modo Aprendiz, apenas é utilizada a **ação predefinitiva,** o que significa que todos os utilizadores verão eficazmente a experiência de controlo.

Mesmo que personalizante seja apenas o _tratamento,_ o mesmo desafio está presente na validação dos dados é bom para a formação do Personalizar. Em vez disso, o modo aprendiz poderia ser utilizado, com 100% do tráfego, e com todos os utilizadores a obterem a experiência de controlo (não afetado).

Uma vez que você tem um caso de uso usando Personalizer e aprender on-line, as experiências A/B permitem-lhe fazer coortes controladas e comparação científica de resultados que podem ser mais complexos do que os sinais usados para recompensas. Uma pergunta exemplo que um teste A/B poderia responder é: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Passos seguintes

* Conheça [os eventos ativos e inativos](concept-active-inactive-events.md)
