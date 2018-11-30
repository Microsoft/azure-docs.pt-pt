---
title: A ciência de 5 dados perguntas - ciência de dados para principiantes - Azure Machine Learning Studio | Documentos da Microsoft
description: Ciência de dados para principiantes é ensina conceitos básicos em 5 vídeos curtos, começando com a respostas de ciência de dados a perguntas de 5. Partir do Azure Machine Learning.
keywords: fazendo a ciência de dados, de iniciantes de ciência de dados, de ciência de dados para principiantes, Noções básicas de ciência de dados, perguntas de ciência de dados, vídeo de ciência de dados, introdução de ciência de dados
services: machine-learning
documentationcenter: na
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: c59b4783c59c44604738a3056b3484e11ff90363
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314171"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data Science for Beginners video 1: The 5 questions data science answers (Vídeo Ciência de Dados para Principiantes: As cinco perguntas às quais a ciência de dados dá resposta)
Obtenha uma introdução rápida à ciência dos dados do *ciência de dados para principiantes* em cinco vídeos curtos de um cientista de dados principais. Estes vídeos são básico, útil, mas se estiver interessado em fazer ciência de dados ou trabalhar com os cientistas de dados.

Este primeiro vídeo é sobre os tipos de perguntas que pode responder a ciência de dados. Para obter o máximo proveito da série, assista a todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Ciência de dados para principiantes* é uma introdução rápida à utilização de ciência de dados total cerca de 25 minutos. Veja todos os cinco vídeos:

* Vídeo 1: O 5 questões de respostas de ciência de dados
* Vídeo 2: [são os seus dados prontos para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg)*
* Vídeo 3: [faça uma pergunta que pode ser respondidas com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcrição: O 5 questões de respostas de ciência de dados
Olá! Bem-vindo à série de vídeos *ciência de dados para principiantes*.

Ciência de dados pode ser intimidante, para que eu o apresentarei os fundamentos sem qualquer equações ou o computador de programação jargão.

Neste primeiro vídeo, vamos falar sobre "os 5 perguntas respostas de ciência de dados."

Ciência de dados utiliza nomes (também conhecido como categorias ou rótulos) e de números para prever as respostas a perguntas.

Ele pode ser surpreendente, mas *existem apenas cinco perguntas que respostas de ciência de dados*:

* É esta A ou B?
* É estranho?
* Quanto – ou – quantos?
* Como é isto organizado?
* O que devo fazer a seguir?

Cada uma dessas perguntas é respondida por uma família separada dos métodos de aprendizado de máquina, denominadas de algoritmos.

É útil pensar num algoritmo como uma receita e os seus dados como os ingredientes. Um algoritmo informa como combinar e misturar os dados para obter uma resposta. Os computadores são como misturador. Que fazem a maior parte do trabalho árduo do algoritmo para e fazem bem rápido.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: É esta A ou B? utiliza algoritmos de classificação
Vamos começar com a questão: É esta A ou B?

![Algoritmos de classificação: É esta A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de duas classes.

É útil para qualquer questão que tem apenas duas possíveis respostas.

Por exemplo:

* Este tire falhará nos seguintes quilómetros 1.000: Sim ou não?
* Que traz a mais clientes: um cupão de US $5 ou um desconto de 25%?

Também pode ser rephrased essa questão para incluir mais de duas opções: É esta A ou B ou C ou 1!d, etc.?  Isso é chamado de classificação multiclasses e é útil quando tem vários — ou vários milhares — possíveis respostas. Classificação multiclasses escolhe o mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: Este é estranho? utiliza algoritmos de detecção de anomalias
A próxima pergunta pode responder a ciência de dados é: É este estranho? Esta pergunta for respondida por uma família de algoritmos de deteção de anomalias de chamada.

![Algoritmos de detecção de anomalias: É este estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se tiver um cartão de crédito, já já se beneficiou da deteção de anomalias. A sua empresa de cartão de crédito analisa os padrões de compra, para que eles podem alertá-lo para possível fraude. Custos que são "estranhas" podem ser uma compra num arquivo onde normalmente não compras ou a compra de um item anormalmente anteriormente.

Essa pergunta pode ser útil em muitas formas. Por exemplo:

* Se tiver um carro com os medidores de pressão, pode querer saber: É deste medidor de pressão de leitura normal?
* Se estiver a monitorizar a internet, gostaria de saber: esta mensagem da internet é típico?

Deteção de anomalias sinaliza eventos inesperados ou invulgares ou comportamentos. Ele dá dicas onde procurar problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: Quanto? ou, como muitos? utiliza algoritmos de regressão
Machine learning também pode preveja a resposta como muito? ou, como muitos? A família de algoritmo que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: quanto? ou, como muitos?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algoritmos de regressão fazer predições de numéricas, tais como:

* Qual será a temperatura a terça-feira seguinte?  
* Qual será minhas vendas do trimestre quarta?

Eles ajudam a responder a quaisquer perguntas que pede-lhe um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: Como é isto organizado? utiliza algoritmos de clustering
Agora, as duas últimas perguntas estão um pouco mais avançado.

Por vezes, pretende compreender a estrutura de um conjunto de dados - como é isto organizado? Para essa pergunta, não é necessário exemplos que já conhece os resultados para.

Existem muitas formas revelar a estrutura dos dados. Uma abordagem é o clustering. Separa dados em natural "clumps," para a interpretação mais fácil. Com o clustering, não há nenhuma resposta correta.

![Algoritmos de clustering: como é isto organizado?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

São exemplos comuns de clusters perguntas:

* Os visualizadores de como os mesmos tipos de filmes?
* Os modelos de impressora falham da mesma forma?

Ao compreender a forma como os dados são organizados, pode compreender melhor – e a prever - comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: O que devo fazer agora? utiliza por reforço algoritmos de aprendizagem
A última pergunta – o que devo fazer agora? – utiliza uma família de algoritmos de aprendizagem por reforço de chamada.

Aprendizagem por reforço foi inspirada por como o cérebro do rats e humanos respondem a sofrer e recompensas. Esses algoritmos aprender com os resultados e decidir qual a ação seguinte.

Normalmente, a aprendizagem por reforço é uma boa opção para sistemas automatizados e que tem de fazer muitas pequenas decisões sem orientação humana.

![Por reforço algoritmos de aprendizagem: o que devo fazer a seguir?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Perguntas responde são sempre sobre que ação deve ser levado - normalmente, por uma máquina ou um robô. Os exemplos são:

* Se eu sou um sistema de controle de temperatura de uma casa: ajustar uma temperatura ou deixá-lo em que é?  
* Se eu for um carro autónomas: uma luz amarela, brake ou acelerar?  
* Para um aspirador de robô: manter vacuuming ou voltar para a estação charging?

Algoritmos de aprendizagem por reforço recolher dados, como está, de aprendizagem de tentativa e erro.

Isso é tudo - pode responder a ciência de dados de perguntas a 5.

## <a name="next-steps"></a>Passos Seguintes
* [Tente uma primeira experimentação de ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
