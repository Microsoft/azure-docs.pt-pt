---
title: 'Estúdio ML (clássico): Avaliação de dados - Azure'
description: Quatro critérios que os seus dados precisam de cumprir para estarem prontos para a ciência dos dados. Este vídeo tem exemplos concretos para ajudar na avaliação básica de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 07db1f1c8021759fbd5702ad9353f74fcd1524c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100516979"
---
# <a name="is-your-data-ready-for-data-science"></a>Os seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Data Science for Beginners series
Saiba como avaliar os seus dados para se certificar de que cumpre critérios básicos para estar pronto para a ciência dos dados.

Para tirar o máximo partido da série, assista a todas. [Vá à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Data Science for Beginners* é uma rápida introdução à ciência dos dados em cinco pequenos vídeos.

* Vídeo 1: [As 5 perguntas sobre as respostas da ciência dos dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: Os seus dados estão prontos para a ciência dos dados?
* Vídeo 3: [Faça uma pergunta que pode responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: [Prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: Os seus dados estão prontos para a ciência dos dados?
Bem-vindos ao "Os seus dados estão prontos para a ciência dos dados?" o segundo vídeo da série *Data Science for Beginners*.  

Antes que a ciência dos dados possa dar-lhe as respostas que deseja, você tem que dar-lhe algumas matérias-primas de alta qualidade para trabalhar. Assim como fazer uma pizza, quanto melhor os ingredientes começar, melhor o produto final. 

## <a name="criteria-for-data"></a>Critérios para os dados
Na ciência dos dados, há certos ingredientes que devem ser reunidos, incluindo:

* Relevante
* Ligada
* Preciso
* O suficiente para trabalhar com

## <a name="is-your-data-relevant"></a>Os seus dados são relevantes?
Portanto, o primeiro ingrediente - precisa de dados que são relevantes.

![Dados relevantes vs. dados irrelevantes - avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

À esquerda, a mesa apresenta o nível de álcool no sangue de sete pessoas testadas fora de um bar de Boston, a média de batimentos dos Red Sox no seu último jogo, e o preço do leite na loja de conveniência mais próxima.

São dados perfeitamente legítimos. A única culpa é que não é relevante. Não há uma relação óbvia entre estes números. Se alguém te der o preço atual do leite e a média de batimentos dos Red Sox, não há como adivinhar o teor de álcool no sangue.

Agora olhe para a mesa à direita. Desta vez, a massa corporal de cada pessoa foi medida, bem como o número de bebidas que beberam.  Os números de cada linha são agora relevantes uns para os outros. Se te desse a minha massa corporal e o número de Margaritas que tive, podias adivinhar o meu teor de álcool no sangue.

## <a name="do-you-have-connected-data"></a>Tem dados ligados?
O próximo ingrediente são dados conectados.

![Dados ligados vs. dados desligados - critérios de dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Aqui estão alguns dados relevantes sobre a qualidade dos hambúrgueres: temperatura da grelha, peso patty e classificação na revista de alimentos local. Mas reparem nas lacunas na mesa à esquerda.

A maioria dos conjuntos de dados estão a perder alguns valores. É comum ter buracos como este e há maneiras de contornar. Mas se faltar muito, os seus dados começam a parecer queijo suíço.

Se olharmos para a mesa à esquerda, há tantos dados em falta, que é difícil encontrar qualquer tipo de relação entre a temperatura da grelha e o peso da patty. Este exemplo mostra dados desligados.

A tabela à direita, no entanto, é completa e completa - um exemplo de dados conectados.

## <a name="is-your-data-accurate"></a>Os seus dados são precisos?
O próximo ingrediente é a precisão. Aqui estão quatro alvos para atingir.

![Dados precisos vs. dados imprecisos - critérios de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Olhe para o alvo no canto superior direito. Há um agrupamento apertado à volta do olho dos touros. Isto, claro, é exato. Estranhamente, na linguagem da ciência dos dados, o desempenho no alvo logo abaixo também é considerado preciso.

Se mapeasses o centro destas flechas, verias que está muito perto do olho dos touros. As setas estão espalhadas à volta do alvo, por isso são consideradas imprecisas, mas estão centradas à volta do olho dos touros, por isso são consideradas precisas.

Agora olhe para o alvo superior esquerdo. Aqui as setas bateram muito perto, um agrupamento apertado. São precisos, mas imprecisos porque o centro está fora dos olhos dos touros. As setas no alvo inferior esquerdo são imprecisas e imprecisas. Este arqueiro precisa de mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Tem dados suficientes para trabalhar?
Finalmente, #4 ingredientes são dados suficientes.

![Tem dados suficientes para análise? Avaliação de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pense em cada ponto de dados na sua mesa como sendo um pincel numa pintura. Se tem apenas alguns, o quadro pode ser confuso- é difícil dizer o que é.

Se adicionar mais pinceladas, então a sua pintura começa a ficar um pouco mais afiada.

Quando mal se tem golpes suficientes, só se vê o suficiente para tomar decisões amplas. É algum lugar que eu queira visitar? Parece brilhante, que parece água limpa - sim, é onde vou de férias.

À medida que adiciona mais dados, a imagem torna-se mais clara e pode tomar decisões mais detalhadas. Agora você pode olhar para os três hotéis na margem esquerda. Nota-se as características arquitetónicas daquele em primeiro plano. Pode até optar por ficar no terceiro andar por causa da vista.

Com dados relevantes, conectados, precisos e suficientes, você tem todos os ingredientes necessários para fazer alguma ciência de dados de alta qualidade.

Certifique-se de conferir mais quatro vídeos em *Data Science para Principiantes* do Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente uma primeira experiência de ciência de dados com o Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](../overview-what-is-azure-ml.md)