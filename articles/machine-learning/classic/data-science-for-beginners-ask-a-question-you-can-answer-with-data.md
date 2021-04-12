---
title: Faça uma pergunta que os dados podem responder - ML Studio (clássico) - Azure
description: Aprenda a formular uma questão aguda da ciência dos dados em Data Science para Principiantes vídeo 3. Inclui uma comparação entre questões de classificação e regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 2ff440801db7ba8c5bfc1785fc7a399a1d773b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517030"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Fazer perguntas que podem ser respondidas com dados
## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Data Science for Beginners series
Aprenda a formular um problema de ciência de dados numa questão em Data Science para Principiantes vídeo 3. Este vídeo inclui uma comparação de perguntas para algoritmos de classificação e regressão.

Para tirar o máximo partido da série, assista a todas. [Vá à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Data Science for Beginners* é uma rápida introdução à ciência dos dados em cinco pequenos vídeos.

* Vídeo 1: [As 5 perguntas sobre as respostas da ciência dos dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: [Os seus dados estão prontos para a ciência dos dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg)*
* Vídeo 3: Faça uma pergunta que pode responder com dados
* Vídeo 4: [Prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcrição: Faça uma pergunta que pode responder com dados
Bem-vindos ao terceiro vídeo da série "Data Science for Beginners".  

Neste, receberá algumas dicas para formular uma pergunta que pode responder com dados.

Pode obter mais deste vídeo, se assistir primeiro aos dois vídeos anteriores desta série: "As 5 perguntas que a ciência dos dados pode responder" e "Os seus dados estão prontos para a ciência dos dados?"

## <a name="ask-a-sharp-question"></a>Faça uma pergunta afiada
Falamos sobre como a ciência dos dados é o processo de usar nomes (também chamados categorias ou rótulos) e números para prever uma resposta a uma pergunta. Mas não pode ser qualquer pergunta. tem que ser uma *pergunta afiada.*

Uma pergunta vaga não tem de ser respondida com um nome ou um número. Uma pergunta afiada deve.

Imagina que encontraste uma lâmpada mágica com um génio que responderá sinceramente a qualquer pergunta que faças. Mas é um génio travesso, que tentará tornar a sua resposta o mais vaga e confusa que puderem. Queres prendê-los com uma pergunta tão hermética que não podem deixar de te dizer o que queres saber.

Se fizesses uma pergunta vaga, como "O que vai acontecer com as minhas ações?", o génio pode responder: "O preço vai mudar". É uma resposta verdadeira, mas não é muito útil.

Mas se fizesses uma pergunta afiada, como "Qual será o preço de venda das minhas ações na próxima semana?", o génio não pode deixar de lhe dar uma resposta específica e prever um preço de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos da sua resposta: Dados-alvo
Assim que formular a sua pergunta, verifique se tem exemplos da resposta nos seus dados.

Se a nossa pergunta é: "Qual será o preço de venda das minhas ações na próxima semana?" então temos que ter certeza que os nossos dados incluem o histórico de preços das ações.

Se a nossa pergunta é: "Qual carro na minha frota vai falhar primeiro?" então temos que garantir que os nossos dados incluem informações sobre falhas anteriores.

![Dados-alvo - exemplos da sua resposta. Formular uma questão de ciência de dados.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Estes exemplos de respostas são chamados de alvo. Um alvo é o que estamos a tentar prever sobre futuros pontos de dados, seja uma categoria ou um número.

Se não tiver dados de alvo, terá de arranjar alguns. Não poderá responder à sua pergunta sem ela.

## <a name="reformulate-your-question"></a>Reformular a sua pergunta
Às vezes pode reformular a sua pergunta para obter uma resposta mais útil.

A pergunta "Este ponto de dados é A ou B?" prevê a categoria (ou nome ou rótulo) de algo. Para responder, usamos um *algoritmo de classificação.*

A pergunta "Quanto?" ou "Quantos?" prevê uma quantidade. Para responder, usamos um *algoritmo de regressão.*

Para ver como podemos transformá-los, vamos olhar para a pergunta: "Que notícias é a mais interessante para este leitor?" Pede uma previsão de uma única escolha de muitas possibilidades - ou seja, "Isto é A ou B ou C ou D?" - e usaria um algoritmo de classificação.

Mas, esta pergunta pode ser mais fácil de responder se a reformular como "Quão interessante é cada história desta lista para este leitor?" Agora pode dar a cada artigo uma pontuação numérica, e depois é fácil identificar o artigo com maior pontuação. Isto é uma reformulação da questão da classificação numa questão de regressão ou quanto?

![Reformular a tua pergunta. Pergunta de classificação vs. pergunta de regressão.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Como fazes uma pergunta é uma pista para qual algoritmo te pode dar uma resposta.

Verá que certas famílias de algoritmos - como as do nosso exemplo de notícias - estão intimamente relacionadas. Pode reformular a sua pergunta para usar o algoritmo que lhe dá a resposta mais útil.

Mas, o mais importante, faça essa pergunta afiada- a pergunta que pode responder com dados. E certifique-se de ter os dados certos para responder.

Falámos de alguns princípios básicos para fazer uma pergunta que podes responder com dados.

Certifique-se de conferir mais vídeos em "Data Science for Beginners" do Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente uma primeira experiência de ciência de dados com o Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](../overview-what-is-azure-ml.md)