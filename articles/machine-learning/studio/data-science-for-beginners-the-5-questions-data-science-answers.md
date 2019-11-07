---
title: Ciência de Dados para Principiantes
titleSuffix: ML Studio (classic) Azure
description: A ciência de dados para iniciantes é ensina conceitos básicos em cinco vídeos curtos, começando com as cinco perguntas que a ciência de dados responde. De Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: b2a91f3df3bab6a2ba10090719d0e614b9d652a3
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621767"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data Science for Beginners video 1: The 5 questions data science answers (Vídeo Ciência de Dados para Principiantes: As cinco perguntas às quais a ciência de dados dá resposta)
Obtenha uma rápida introdução à ciência de dados da *ciência de dados para iniciantes* em cinco vídeos curtos de um cientista de dados principal. Esses vídeos são básicos, mas úteis, independentemente de você estar interessado em fazer ciência de dados ou trabalhar com cientistas de dados.

Este primeiro vídeo é sobre os tipos de perguntas que a ciência de dados pode responder. Para obter o máximo da série, Assista a todos eles. [Ir para a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
A *ciência de dados para iniciantes* é uma breve introdução à ciência de dados levando cerca de 25 minutos no total. Confira todos os cinco vídeos:

* Vídeo 1: as cinco perguntas que a ciência de dados responde
* Vídeo 2: [seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Vídeo 3: [faça uma pergunta que você possa responder com os dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [copiar o trabalho de outras pessoas para a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minutos e 18 segundos)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcrição: as cinco perguntas que a ciência de dados responde
Oi! Bem-vindo à série de vídeos *ciência de dados para iniciantes*.

A ciência de dados pode ser intimidadora, portanto, apresentarei os conceitos básicos aqui sem nenhuma equação ou jargão de programação de computador.

Neste primeiro vídeo, falaremos sobre "as cinco perguntas que a ciência de dados responde".

A ciência de dados usa números e nomes (também conhecidos como categorias ou rótulos) para prever respostas para perguntas.

Ele pode surpreender você, mas *há apenas cinco perguntas que a ciência de dados responde*:

* Isso é A ou B?
* Isso é estranho?
* Quanto – ou – quantos?
* Como isso é organizado?
* O que devo fazer em seguida?

Cada uma dessas perguntas é respondida por uma família separada de métodos de aprendizado de máquina, chamados de algoritmos.

É útil pensar em um algoritmo como uma receita e seus dados como os ingredientes. Um algoritmo informa como combinar e misturar os dados para obter uma resposta. Os computadores são como um misturador. Eles fazem a maior parte do trabalho árduo do algoritmo para você e eles fazem isso muito rápido.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: é A ou B? usa algoritmos de classificação
Vamos começar com a pergunta: esta é A ou B?

![Algoritmos de classificação: é A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de duas classes.

É útil para qualquer pergunta que tenha apenas duas respostas possíveis.

Por exemplo:

* Haverá falha neste aro nas próximas 1.000 milhas: Sim ou não?
* O que traz mais clientes: um cupom de $5 ou um desconto de 25%?

Essa pergunta também pode ser reformulada para incluir mais de duas opções: é A ou B ou C ou D, etc.?  Isso é chamado de classificação multiclasse e é útil quando você tem vários, ou milhares, de respostas possíveis. Classificação multiclasse escolhe a mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: isso é estranho? usa algoritmos de detecção de anomalias
A próxima pergunta que a ciência de dados pode responder é: isso é estranho? Essa pergunta é respondida por uma família de algoritmos chamada detecção de anomalias.

![Algoritmos de detecção de anomalias: isso é estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se você tiver um cartão de crédito, já terá se beneficiado da detecção de anomalias. Sua empresa de cartão de crédito analisa seus padrões de compra, para que eles possam alertá-lo sobre possíveis fraudes. Os encargos que são "estranhos" podem ser uma compra em uma loja em que você normalmente não faz compras ou comprando um item de preço incomum.

Essa pergunta pode ser útil de várias maneiras. Por exemplo:

* Se você tiver um carro com medidores de pressão, talvez queira saber: Este medidor de pressão está lendo normal?
* Se você estiver monitorando a Internet, convém saber: esta é a mensagem da Internet típica?

Detecção de anomalias sinaliza eventos ou comportamentos inesperados ou incomuns. Ele fornece dicas de onde procurar problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: o quanto? ou quantos? usa algoritmos de regressão
O Machine Learning também pode prever a resposta para o quanto? ou quantos? A família de algoritmos que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: o quanto? ou quantos?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Os algoritmos de regressão fazem previsões numéricas, como:

* Qual será a temperatura na próxima terça-feira?  
* Quais serão as minhas vendas do quarto trimestre?

Eles ajudam a responder a qualquer pergunta que solicite um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: como isso é organizado? usa algoritmos de clustering
Agora, as duas últimas perguntas são um pouco mais avançadas.

Às vezes, você deseja entender a estrutura de um conjunto de dados – como isso é organizado? Para essa pergunta, você não tem exemplos dos quais já conhece os resultados.

Há várias maneiras de separar a estrutura dos dados. Uma abordagem é o clustering. Ele separa os dados em "naturais" naturais para facilitar a interpretação. Com o clustering, não há uma resposta certa.

![Algoritmos de clustering: como isso é organizado?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Exemplos comuns de perguntas de clustering são:

* Quais visualizadores gostam dos mesmos tipos de filmes?
* Quais modelos de impressora falham da mesma maneira?

Ao entender como os dados são organizados, você pode entender melhor e prever comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: o que devo fazer agora? usa algoritmos de aprendizado reforço
A última pergunta – o que devo fazer agora? – usa uma família de algoritmos chamada reforço Learning.

O aprendizado de reforço foi inspirado por como os cérebros de ratos e humanos respondem a sofrimento e recompensas. Esses algoritmos aprendem com resultados e decidem sobre a próxima ação.

Normalmente, o aprendizado de reforço é uma boa opção para sistemas automatizados que precisam tomar muitas decisões pequenas sem orientação humana.

![Algoritmos de aprendizado de reforço: o que devo fazer em seguida?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

As perguntas que ele responde são sempre sobre a ação que deve ser tomada – geralmente por um computador ou um robô. Os exemplos são:

* Se eu for um sistema de controle de temperatura para uma casa: ajustar a temperatura ou deixá-lo onde está?  
* Se eu for um carro de autoatendimento: a uma luz amarela, freio ou acelere?  
* Para uma vácuo de robô: continuar a se aspirar ou voltar para a estação de cobrança?

Os algoritmos de aprendizado de reforço coletam dados conforme eles vão, aprendendo com a avaliação e o erro.

É isso, as cinco perguntas que a ciência de dados pode responder.

## <a name="next-steps"></a>Passos seguintes
* [Experimente um primeiro experimento de ciência de dados com Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução a Machine Learning em Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
