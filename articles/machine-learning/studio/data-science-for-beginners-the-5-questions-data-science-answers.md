---
title: Ciência de Dados para Principiantes
titleSuffix: ML Studio (classic) - Azure
description: Data Science for Beginners é ensina conceitos básicos em 5 pequenos vídeos, começando com As 5 Perguntas Respostas de Ciência de Dados. Da Aprendizagem automática Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 350363641056802e09eea90c85b4ccf8e33d9051
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930153"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data Science for Beginners video 1: The 5 questions data science answers (Vídeo Ciência de Dados para Principiantes: As cinco perguntas às quais a ciência de dados dá resposta)
Obtenha uma introdução rápida à ciência de dados da *Data Science para Principiantes* em cinco pequenos vídeos de um cientista de dados de topo. Estes vídeos são básicos, mas úteis, quer esteja interessado em fazer ciência de dados ou em trabalhar com cientistas de dados.

Este primeiro vídeo é sobre o tipo de perguntas que a ciência dos dados pode responder. Para tirar o máximo partido da série, vê-los a todos. [Vá à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Data Science for Beginners* é uma introdução rápida à ciência dos dados demorando cerca de 25 minutos no total. Confira os cinco vídeos:

* Vídeo 1: As 5 perguntas sobre respostas da ciência dos dados
* Vídeo 2: [Os seus dados estão prontos para a ciência](data-science-for-beginners-is-your-data-ready-for-data-science.md) dos dados? *(4 min 56 seg)*
* Vídeo 3: [Faça uma pergunta que pode responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: [Prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) de dados *(3 min 18 seg)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcrição: As 5 perguntas respostas da ciência dos dados
Olá! Bem-vindos à série de vídeo *Data Science for Beginners*.

A Ciência dos Dados pode ser intimidante, por isso vou introduzir o básico aqui sem equações ou jargão de programação de computadores.

Neste primeiro vídeo, falaremos sobre "As 5 perguntas respostas da ciência dos dados".

Data Science usa números e nomes (também conhecidos como categorias ou rótulos) para prever respostas a perguntas.

Pode surpreendê-lo, mas *há apenas cinco perguntas que a ciência dos dados responde:*

* Isto é A ou B?
* Isto é estranho?
* Quanto – ou – Quantos?
* Como é que isto é organizado?
* O que devo fazer a seguir?

Cada uma destas perguntas é respondida por uma família separada de métodos de aprendizagem automática, chamados algoritmos.

É útil pensar num algoritmo como uma receita e os seus dados como ingredientes. Um algoritmo diz como combinar e misturar os dados para obter uma resposta. Os computadores são como um liquidificador. Fazem a maior parte do trabalho árduo do algoritmo por ti e fazem-no muito depressa.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: Isto é A ou B? usa algoritmos de classificação
Vamos começar com a pergunta: isto é A ou B?

![Algoritmos de classificação: Isto é A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Esta família de algoritmos é chamada classificação de duas classes.

É útil para qualquer pergunta que tenha apenas duas respostas possíveis.

Por exemplo:

* Este pneu vai falhar nos próximos 1.600 km: Sim ou não?
* O que traz mais clientes: um cupão de 5 dólares ou um desconto de 25%?

Esta pergunta também pode ser reformulada para incluir mais de duas opções: Isto é A ou B ou C ou D, etc.?  Isto chama-se classificação multiclasse e é útil quando se tem várias - ou várias mil - respostas possíveis. A classificação multiclasse escolhe a mais provável.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: Isto é estranho? usa algoritmos de deteção de anomalias
A próxima pergunta que a ciência dos dados pode responder é: Isto é estranho? Esta pergunta é respondida por uma família de algoritmos chamadodetecção de anomalias.

![Algoritmos de deteção de anomalias: Isto é estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se tem um cartão de crédito, já beneficiou da deteção de anomalias. A empresa de cartões de crédito analisa os seus padrões de compra, para que possam alertá-lo para possíveis fraudes. As acusações que são "estranhas" podem ser uma compra numa loja onde normalmente não se compra ou compra um item anormalmente caro.

Esta questão pode ser útil de muitas maneiras. Por exemplo:

* Se tiver um carro com medidor de pressão, talvez queira saber: Este medidor de pressão está a ler normalmente?
* Se está a monitorizar a internet, gostaria de saber: Esta mensagem da internet é típica?

A deteção de anomalias sinaliza eventos ou comportamentos inesperados ou incomuns. Dá pistas onde procurar problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: Quanto? ou quantos? usa algoritmos de regressão
A aprendizagem automática também pode prever a resposta para quanto? ou quantos? A família algoritmo que responde a esta pergunta chama-se regressão.

![Algoritmos de regressão: Quanto? ou quantos?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Os algoritmos de regressão fazem previsões numéricas, tais como:

* Qual será a temperatura na próxima terça-feira?  
* Quais serão as minhas vendas do quarto trimestre?

Ajudam a responder a qualquer pergunta que peça um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: Como é que isto é organizado? usa algoritmos de clustering
Agora as duas últimas perguntas são um pouco mais avançadas.

Às vezes quer entender a estrutura de um conjunto de dados - Como é que isto é organizado? Para esta pergunta, não tem exemplos para os quais já conhece os resultados.

Há muitas maneiras de provocar a estrutura dos dados. Uma abordagem é o agrupamento. Separa os dados em "aglomerados" naturais, para uma interpretação mais fácil. Com o agrupamento, não há uma resposta certa.

![Algoritmos de agrupamento: Como é que isto é organizado?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Exemplos comuns de questões de agrupamento são:

* Que espectadores gostam dos mesmos tipos de filmes?
* Que modelos de impressora falham da mesma forma?

Ao entender como os dados são organizados, pode compreender melhor - e prever - comportamentos e eventos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: O que devo fazer agora? usa algoritmos de aprendizagem de reforço
A última pergunta - O que devo fazer agora? – usa uma família de algoritmos chamados aprendizagem de reforço.

A aprendizagem do reforço foi inspirada na forma como os cérebros de ratos e humanos respondem aos castigos e recompensas. Estes algoritmos aprendem com os resultados e decidem sobre a próxima ação.

Tipicamente, a aprendizagem de reforço é um bom ajuste para sistemas automatizados que têm que tomar muitas pequenas decisões sem orientação humana.

![Algoritmos de aprendizagem de reforço: O que devo fazer a seguir?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

As perguntas que responde são sempre sobre que medidas devem ser tomadas - geralmente por uma máquina ou um robô. Os exemplos são:

* Se eu sou um sistema de controlo de temperatura para uma casa: ajustar a temperatura ou deixá-la onde está?  
* Se eu sou um carro autónomo: com uma luz amarela, travão ou acelera?  
* Para um aspirador de robô: Continuar a aspirar ou voltar para a estação de carregamento?

Algoritmos de aprendizagem de reforço recolhem dados à medida que vão, aprendendo com o julgamento e o erro.

As 5 perguntas que a ciência dos dados pode responder.

## <a name="next-steps"></a>Passos seguintes
* [Experimente uma primeira experiência de ciência de dados com o Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/overview-what-is-azure-ml)
