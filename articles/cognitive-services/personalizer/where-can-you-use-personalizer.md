---
title: Avaliação do cenário - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer podem ser aplicados em qualquer situação em que seu aplicativo pode selecionar a itens da direita, a ação ou o produto para exibir – para melhorar a experiência, obter melhores resultados empresariais ou aumentar a produtividade.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: e51ef9afd0e49b690a4f9cab09fdfbd3e86eee66
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441051"
---
# <a name="where-can-you-use-personalizer"></a>Onde pode utilizar o Personalizador?

Utilize Personalizer em qualquer situação em que a sua aplicação precisa para selecionar a itens da direita, a ação ou o produto para apresentar – fim de melhorar a experiência, obter melhores resultados empresariais ou aumentar a produtividade. 

Personalizer utiliza machine learning para selecionar a ação para mostrar o utilizador. A seleção pode variar significativamente, consoante a quantidade, a qualidade e a distribuição dos dados enviados para o serviço.

### <a name="checklist-for-applying-personalizer"></a>Lista de verificação para a aplicação de Personalizer


Pode aplicar Personalizer em situações em que:

* Tem uma empresa ou o objetivo de Facilidade de utilização para a sua aplicação.
* Tem um local na sua aplicação em que a tomar uma decisão contextual do que mostrar para os utilizadores irão melhorar esse objetivo.
* A melhor opção pode e deve ser aprendida com a pontuação de recompensa de comportamento e total de utilizador coletiva.
* Segue-se a utilização do machine learning para personalização [diretrizes de utilização responsável](ethics-responsible-use.md) e opções que escolheu.
* A decisão contextual pode ser expresso como a classificação a melhor opção (ação) a partir de um conjunto limitado de opções.
* A eficiência com que a escolha classificada trabalhada para a sua aplicação pode ser determinada pelo medição algum aspecto do comportamento do usuário e expressá-lo num _recompensar pontuação_. Este é um número entre -1 e 1.
* A pontuação de recompensa não leva em muitos fatores externos ou confusão. A duração de experimentação é baixa o suficiente para que a pontuação de recompensa pode ser computada, enquanto ainda é relevante.
* Pode expressar o contexto para a classificação como uma lista de, pelo menos, 5 [funcionalidades](concepts-features.md) que pensa que ajudaria a escolha certa e que não inclui informações de identificação pessoal. (PII).
* Tem informações sobre cada opção de conteúdo, _ação_, como uma lista de, pelo menos, 5 [funcionalidades](concepts-features.md) que considere ajuda Personalizer tornará a escolha certa.
* Seu aplicativo pode reter os dados de longa duração suficiente para acumular um histórico de interações, pelo menos, 100 000.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>As considerações para aplicar Personalizer de aprendizagem automática

Personalizer baseia-se por reforço learning, uma abordagem ao machine learning, ou seja, ensinados por comentários dá-lhe. 

Personalizer aprenderá melhor em situações em que:

* Não existe suficiente eventos para ficar por cima de personalização ideal, se o problema drifts ao longo do tempo (por exemplo, as preferências em notícias ou em forma de mensagens em fila). Personalizer irá adaptar-se a mudança contínua no mundo real, mas os resultados não serão ideais se não houver suficiente eventos e os dados de aprender com a descoberta e se concentrar em novos padrões. Deve escolher um caso de utilização que ocorre com frequência suficiente. Considere busca de casos de utilização de isso acontecer, pelo menos, 500 vezes por dia.
* Contexto e ações terá uma parte suficiente [funcionalidades](concepts-features.md) para facilitar a aprendizagem.
* Há menos de 50 ações para classificação por chamada.
* As definições de retenção de dados permitem Personalizer recolher dados suficientes para realizar avaliações offline e a otimização de política. Isto é, normalmente, os pontos de dados, pelo menos, 50 000.

## <a name="monitor-effectiveness-of-personalizer"></a>Eficácia de monitor de Personalizer

Pode monitorizar a eficácia da Personalizer periodicamente, executando [avaliações offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Utilizar Personalizer com motores de recomendações

Muitas empresas utilizam motores de recomendações, ferramentas de marketing e campaign, segmentação da audiência e clustering, filtragem em colaboração e outros meios de recomendar produtos a partir de um catálogo de grandes aos clientes.

O [repositório do GitHub do Microsoft Recommenders](https://github.com/Microsoft/Recommenders) fornece exemplos e melhores práticas para a criação de sistemas de recomendação, fornecidos como blocos de notas do Jupyter. Fornece exemplos de trabalho de preparação de dados, a criação de modelos, avaliar, Otimização e operacionalizar os mecanismos de recomendação, para muitas abordagens comuns incluindo xDeepFM, RAE de, ALS, RBM, DKN.

Personalizer pode trabalhar com um motor de recomendação quando estiver presente.

* Motores de recomendações tirar grandes quantidades de itens (por exemplo, 500 000) e recomenda um subconjunto (por exemplo, os primeiros 20) de centenas ou milhares de opções.
* Personalizer usa um pequeno número de ações com muitas informações sobre eles e classifica-os em tempo real para um determinado contexto sofisticado, embora a maioria dos motores de recomendações usar apenas alguns atributos sobre utilizadores, produtos e suas interações.
* Personalizer destina-se de forma autónoma explorar as preferências do usuário o tempo todo, que irá produzir resultados melhores, onde conteúdo está mudando rapidamente, como notícias, eventos em direto, live conteúdo da Comunidade, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é utilizar a saída de um motor de recomendação (por exemplo, os 20 melhores produtos de um determinado cliente) e utilizá-lo como as ações de entrada para Personalizer.

## <a name="next-steps"></a>Passos Seguintes

[Ética e utiliza responsável](ethics-responsible-use.md).