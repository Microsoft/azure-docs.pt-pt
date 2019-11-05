---
title: Fazer com que os dados da pergunta possam responder
titleSuffix: Azure Machine Learning Studio (classic)
description: Saiba como formular uma pergunta de ciência de dados nítida no vídeo ciência de dados para iniciantes 3. Inclui uma comparação de perguntas de classificação e regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 7f0064d30c96a143dd2e53637a681e4a7ee0bc30
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493162"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Fazer perguntas que podem ser respondidas com dados
## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: série de ciência de dados para iniciantes
Saiba como formular um problema de ciência de dados em uma pergunta no vídeo ciência de dados para iniciantes 3. Este vídeo inclui uma comparação de perguntas para algoritmos de classificação e regressão.

Para obter o máximo da série, Assista a todos eles. [Ir para a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
A *ciência de dados para iniciantes* é uma rápida introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [as cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Vídeo 2: [seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Vídeo 3: faça uma pergunta que você possa responder com os dados
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [copiar o trabalho de outras pessoas para a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minutos e 18 segundos)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcrição: faça uma pergunta que você possa responder com os dados
Bem-vindo ao terceiro vídeo da série "ciência de dados para iniciantes".  

Neste, você obterá algumas dicas para formular uma pergunta que você pode responder com os dados.

Você pode tirar o máximo proveito deste vídeo, se você observar primeiro os dois vídeos anteriores nesta série: "as cinco perguntas que a ciência de dados pode responder" e "seus dados estão prontos para a ciência de dados?"

## <a name="ask-a-sharp-question"></a>Faça uma pergunta nítida
Falamos sobre como a ciência de dados é o processo de usar nomes (também chamados de categorias ou rótulos) e números para prever uma resposta para uma pergunta. Mas não pode ser apenas qualquer pergunta; Ele precisa ser uma *pergunta mais nítida.*

Uma pergunta vaga não precisa ser respondida com um nome ou um número. Uma pergunta precisa.

Imagine que você tenha encontrado uma lâmpada mágica com um gênio que responderá a qualquer pergunta que você pergunte. Mas é um gênio gênio, que tentará fazer suas respostas como vagas e confusas, uma vez que elas podem se afastar. Você deseja fixá-los com uma pergunta tão rígida que eles não podem ajudar, mas informam o que você deseja saber.

Se você fosse fazer uma pergunta vaga, como "o que vai acontecer com meu estoque?", o gênio pode responder, "o preço mudará". Essa é uma resposta confiável, mas não é muito útil.

Mas se você fizer uma pergunta mais nítida, como "qual será o preço de venda do meu estoque na próxima semana?", o gênio não pode ajudar, mas lhe dá uma resposta específica e prevê um preço de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos de sua resposta: dados de destino
Depois de formular sua pergunta, verifique se você tem exemplos de resposta em seus dados.

Se nossa pergunta for "qual será o preço de venda do meu estoque na próxima semana?" em seguida, temos que garantir que nossos dados incluam o histórico de preços de ações.

Se nossa pergunta for "Qual carro em minha frota vai falhar primeiro?" em seguida, temos que garantir que nossos dados incluam informações sobre falhas anteriores.

![Dados de destino-exemplos de sua resposta. Formular uma pergunta de ciência de dados.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Esses exemplos de respostas são chamados de destino. Um destino é o que estamos tentando prever sobre pontos de dados futuros, seja uma categoria ou um número.

Se você não tiver dados de destino, precisará obter alguns. Você não poderá responder à sua pergunta sem ele.

## <a name="reformulate-your-question"></a>Reformular sua pergunta
Às vezes, você pode refazer sua pergunta para obter uma resposta mais útil.

A pergunta "é este ponto de dados A ou B?" prevê a categoria (ou nome ou rótulo) de algo. Para responder, usamos um algoritmo de *classificação*.

A pergunta "o quanto?" ou "Quantos?" prevê um valor. Para responder, usamos um *algoritmo de regressão*.

Para ver como podemos transformá-los, vamos examinar a pergunta "Qual história de notícias é o mais interessante para esse leitor?" Ele solicita uma previsão de uma única escolha de várias possibilidades – em outras palavras, "é A ou B ou C ou D?" -e usaria um algoritmo de classificação.

No entanto, essa pergunta pode ser mais fácil de responder se você a repalavras como "quão interessante é cada história desta lista para este leitor?" Agora você pode dar a cada artigo uma pontuação numérica e, em seguida, é fácil identificar o artigo de pontuação mais alta. Esta é uma reformulação da pergunta de classificação em uma pergunta de regressão ou de quanto?

![Reformule sua pergunta. Pergunta de classificação versus perguntas de regressão.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

A maneira de fazer uma pergunta é uma pista de qual algoritmo pode lhe dar uma resposta.

Você descobrirá que determinadas famílias de algoritmos, como as do nosso exemplo de história de notícias, estão relacionadas. Você pode reformular sua pergunta para usar o algoritmo que oferece a resposta mais útil.

Mas, o mais importante, faça uma pergunta mais nítida – a pergunta que você pode responder com os dados. E verifique se você tem os dados corretos para respondê-lo.

Falamos sobre alguns princípios básicos para fazer uma pergunta que você pode responder com os dados.

Certifique-se de conferir os outros vídeos em "ciência de dados para iniciantes" em Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente um primeiro experimento de ciência de dados com Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução a Machine Learning em Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
