---
title: Avaliação de dados
titleSuffix: ML Studio (classic) Azure
description: Quatro critérios que seus dados precisam cumprir para estar prontos para a ciência de dados. Este vídeo tem exemplos concretos para ajudar com a avaliação básica de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: f01a2f6e8d219d4b8eee005087d8331b117ba7db
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621790"
---
# <a name="is-your-data-ready-for-data-science"></a>Os seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: série de ciência de dados para iniciantes
Saiba como avaliar seus dados para certificar-se de que ele atende aos critérios básicos para estar pronto para a ciência de dados.

Para obter o máximo da série, Assista a todos eles. [Ir para a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
A *ciência de dados para iniciantes* é uma rápida introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [as cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Vídeo 2: seus dados estão prontos para a ciência de dados?
* Vídeo 3: [faça uma pergunta que você possa responder com os dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Vídeo 5: [copiar o trabalho de outras pessoas para a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minutos e 18 segundos)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: seus dados estão prontos para a ciência de dados?
Bem-vindo "seus dados estão prontos para a ciência de dados?" o segundo vídeo na série de *ciência de dados para iniciantes*.  

Antes que a ciência de dados possa fornecer as respostas desejadas, você precisa dar a ela alguns materiais brutos de alta qualidade com os quais trabalhar. Assim como faz uma pizza, quanto melhor os ingredientes com os quais você começa, melhor o produto final. 

## <a name="criteria-for-data"></a>Critérios para dados
Na ciência de dados, há determinados ingredientes que devem ser reunidos, incluindo:

* Relativas
* Ligada
* Modo
* Suficiente para trabalhar com

## <a name="is-your-data-relevant"></a>Seus dados são relevantes?
Portanto, o primeiro ingrediente – você precisa de dados relevantes.

![Dados relevantes versus dados irrelevantes – avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

À esquerda, a tabela apresenta o nível de álcool de sangue de sete pessoas testadas fora de uma barra de Boston, a média de rebatidas do Red Sox em seu último jogo e o preço do leite na loja de conveniência mais próxima.

Esses são os dados perfeitamente legítimos. A única falha é que não é relevante. Não há nenhuma relação óbvia entre esses números. Se alguém lhe forneceu o preço atual do leite e a média de rebatidas do Red Sox, não há como adivinhar o conteúdo de álcool de sangue.

Agora, examine a tabela à direita. Desta vez, o corpo em massa de cada pessoa foi medido, bem como o número de bebidas que tinham.  Os números em cada linha agora são relevantes entre si. Se eu tiver dado a você o meu corpo de massa e o número de margaritas que tive, você poderia fazer uma estimativa em meu conteúdo de álcool de sangue.

## <a name="do-you-have-connected-data"></a>Você tem dados conectados?
O próximo ingrediente é dados conectados.

![Dados conectados vs. dados desconectados – critérios de dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Aqui estão alguns dados relevantes sobre a qualidade do hambúrgueres: a temperatura da grelha, o peso do hambúrguer e a classificação na revista de alimentos local. Mas observe as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados não tem alguns valores. É comum ter buracos como esse e há maneiras de solucioná-los. Mas se houver muita falta, seus dados começarão a se parecer com queijo suíço.

Se você olhar a tabela à esquerda, há muito dados ausentes, é difícil criar qualquer tipo de relação entre a temperatura de grelha e o peso de hambúrguer. Este exemplo mostra dados desconectados.

A tabela à direita, no entanto, está completa e completa-um exemplo de dados conectados.

## <a name="is-your-data-accurate"></a>Seus dados são precisos?
O próximo ingrediente é a precisão. Aqui estão quatro destinos para atingir.

![Dados precisos versus critérios de dados de dados imprecisos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Examine o destino no canto superior direito. Há um pequeno agrupamento em torno do olho do Bull. Isso, é claro, é preciso. Por estranho, na linguagem de ciência de dados, o desempenho no destino logo abaixo também é considerado preciso.

Se você mapeou o centro dessas setas, verá que ela está muito perto do olho de Bull. As setas são espalhadas em todo o destino, portanto, são consideradas imprecisas, mas estão centralizadas em todo o olho, para que elas sejam consideradas precisas.

Agora, observe o destino superior esquerdo. Aqui, as setas se aproximam muito bem, um agrupamento rígido. Eles são precisos, mas não são precisos porque o centro está longe do olho. As setas no destino inferior esquerdo são imprecisas e imprecisas. Esse arqueiro precisa de mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Você tem dados suficientes para trabalhar?
Por fim, os #4 de ingrediente são dados suficientes.

![Você tem dados suficientes para análise? Avaliação de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Considere cada ponto de dados em sua tabela como sendo um traço de pincel em uma pintura. Se você tiver apenas alguns deles, a pintura poderá ser difusa. é difícil dizer o que é.

Se você adicionar mais alguns traços de pincel, sua pintura começará a ficar um pouco mais nítido.

Quando você não tem traços suficientes, só vê o suficiente para tomar algumas decisões amplas. É em algum lugar que eu talvez queira visitar? Parece claro, parece uma água limpa – Sim, é aí que estou saindo de férias.

À medida que você adiciona mais dados, a imagem fica mais clara e você pode tomar decisões mais detalhadas. Agora você pode examinar os três Hotéis no banco esquerdo. Você pode observar os recursos arquitetônicos do um no primeiro plano. Você pode até mesmo optar por permanecer no terceiro andar por causa da exibição.

Com os dados que são relevantes, conectados, precisos e suficientes, você tem todos os ingredientes necessários para fazer uma ciência de dados de alta qualidade.

Certifique-se de conferir os outros quatro vídeos em *ciência de dados para iniciantes* de Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente um primeiro experimento de ciência de dados com Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução a Machine Learning em Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
