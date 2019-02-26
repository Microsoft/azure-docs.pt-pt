---
title: Faça uma pergunta podem responder a dados
titleSuffix: Azure Machine Learning Studio
description: Aprenda a formular uma pergunta de ciência de dados pequenos em ciência de dados para principiantes 3 vídeo. Inclui uma comparação das perguntas sobre classificação e regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/03/2018
ms.openlocfilehash: fcd79b94c1a32fa912f00df616787f8859005895
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820179"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Fazer perguntas que podem ser respondidas com dados
## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Ciência de dados para a série de iniciantes
Aprenda a formular um problema de ciência de dados numa pergunta em ciência de dados para principiantes 3 vídeo. Este vídeo inclui uma comparação das perguntas para algoritmos de classificação e regressão.

Para obter o máximo proveito da série, assista a todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Ciência de dados para principiantes* é uma introdução rápida à ciência dos dados em cinco vídeos curtos.

* Vídeo 1: [Respostas de ciência de dados de questões do 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: [Seus dados está pronto para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg)*
* Vídeo 3: Fazer perguntas que podem ser respondidas com dados
* Vídeo 4: [Prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcrição: Fazer perguntas que podem ser respondidas com dados
Bem-vindo ao terceiro vídeo da série "Ciência de dados para principiantes."  

Este aqui, obterá algumas dicas para formular uma pergunta que pode ser respondidas com dados.

Poderá receber mais fora neste vídeo, se primeiro, Assista aos vídeos anteriores dois desta série: "A ciência de dados de 5 perguntas pode responder a" e "É seus dados estão prontos para ciência de dados?"

## <a name="ask-a-sharp-question"></a>Faça uma pergunta sharp
Falamos sobre como a ciência de dados é o processo de uso (também denominado categorias ou rótulos) e os números para prever uma resposta a uma pergunta. Mas não pode ser qualquer pergunta; tem de ser um *sharp pergunta.*

Não tem uma pergunta vaga de ser respondidas com um nome ou um número. Tem de ser uma pergunta sharp.

Imagine que encontrou uma mágica lamp com um genie que puder sustentar vai responder a qualquer pergunta. Mas é um genie mischievous, e ele vai tentar tornar sua resposta mais vagas e confusos, como ele pode se contentar com. Pretende afixar em contato com ele para baixo com uma pergunta tão airtight que ele não pode ajudar a mas dizer o que deseja saber.

Se fosse fazer uma pergunta de vaga, como "O que vai acontecer com meu stock?", poderá responder a genie, "o preço será alterado". Essa é uma resposta truthful, mas não é muito útil.

Mas se tivesse que fazer uma pergunta sharp, como "O que o preço de venda de meu stock serão próxima semana?", o genie não pode ajudar a mas dão-lhe um específico responder e prever o preço de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos da sua resposta: Dados de destino
Depois de formular a sua pergunta, verifique se tem exemplos da resposta nos seus dados.

Se nossa pergunta é "O que o preço de venda de meu stock serão próxima semana?" em seguida, temos de certificar-se de que nossos dados incluem o histórico do preço das ações.

Se nossa pergunta é "qual carro em meu frota vai falhar em primeiro lugar?" em seguida, temos de certificar-se de que nossos dados incluem informações sobre falhas anteriores.

![Dados de destino - exemplos da sua resposta. Formule uma pergunta de ciência de dados.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Estes exemplos de respostas são chamados de um destino. Um destino é o que estamos a tentar prever sobre os pontos de dados futuros, quer se trate de uma categoria ou um número.

Se não tiver quaisquer dados de destino, terá de obter algumas. Não será capaz de responder à sua pergunta sem ele.

## <a name="reformulate-your-question"></a>Reformulate sua pergunta
Por vezes, pode reword sua pergunta para receber uma resposta mais úteis.

A pergunta "É este ponto de dados A ou B?" prevê a categoria (nome ou etiqueta) de algo. Para respondê-lo, utilizamos um *algoritmo de classificação*.

A pergunta "Quanto?" ou "Quantos?" prevê uma quantidade. Para respondê-la, usamos uma *algoritmo de regressão*.

Para ver como podemos podem transformar esses, vamos examinar a pergunta, "que história de notícias é o mais interessante para este leitor?" Ele solicita uma predição de uma única opção de muitas possibilidades – em outras palavras "É esta A ou B ou C ou D?" - e usaria um algoritmo de classificação.

No entanto, essa pergunta pode ser mais fácil de responder, se reword-la como "como interessante é cada história esta lista para este leitor?" Agora pode dar a cada artigo uma pontuação numérica e, em seguida, é fácil identificar o artigo de pontuação mais alta. Este é um reformular da pergunta classificação numa questão de regressão ou quanto?

![Reformulate sua pergunta. Pergunta de classificação versus pergunta de regressão.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Como faça que uma pergunta é uma pista para quais algoritmos pode fornecer uma resposta.

Descobrirá que certas famílias de algoritmos - como os que no nosso exemplo de história de notícias - estão intimamente relacionadas. Pode reformulate sua pergunta para utilizar o algoritmo que lhe dá a resposta mais úteis.

Mas, mais importante, fazer essa pergunta sharp - a pergunta que podem ser respondidas com dados. E certifique-se de que tem os dados adequados respondê-la.

Falamos sobre alguns princípios básicos para fazer uma pergunta que pode ser respondidas com dados.

Certifique-se de que consulta outros vídeos em "Dados ciência para principiantes" do Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Passos Seguintes
* [Tente uma primeira experimentação de ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
