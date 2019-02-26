---
title: Avaliação de dados
titleSuffix: Azure Machine Learning Studio
description: Critérios de quatro que seus dados têm de cumprir para estar pronto para ciência de dados. Este vídeo tem exemplos concretos para ajudar na avaliação de dados básicos.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 01/03/2018
ms.openlocfilehash: 9cd2d1264d08887f75eaf7ae624b6a45ec8d6a34
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817580"
---
# <a name="is-your-data-ready-for-data-science"></a>Os seus dados estão prontos para a ciência de dados?
## <a name="video-2-data-science-for-beginners-series"></a>Vídeo 2: Ciência de dados para a série de iniciantes
Saiba como avaliar os seus dados para se certificar de que cumpre os critérios básicos para estar preparado para ciência de dados.

Para obter o máximo proveito da série, assista a todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Ciência de dados para principiantes* é uma introdução rápida à ciência dos dados em cinco vídeos curtos.

* Vídeo 1: [Respostas de ciência de dados de questões do 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: Os seus dados estão prontos para a ciência de dados?
* Vídeo 3: [Faça uma pergunta que pode ser respondidas com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: [Prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg)*
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcrição: Os seus dados estão prontos para a ciência de dados?
Bem-vindo ao "São os seus dados prontos para ciência de dados?" o segundo vídeo da série *ciência de dados para principiantes*.  

Antes de ciência de dados pode fornecer-lhe as respostas que pretende, terá de atribuir-lhe alguns materiais de raw de alta qualidade para trabalhar com. Como fazer uma pizza, melhor os ingredientes que iniciar com melhor o produto final. 

## <a name="criteria-for-data"></a>Critérios dos dados
Ciência de dados, há determinadas ingredientes que têm de ser extraídos em conjunto incluindo:

* Relevantes
* Ligado
* Precisas
* Suficiente para trabalhar com

## <a name="is-your-data-relevant"></a>São os seus dados relevantes?
Por isso o ingrediente primeiro - terá dados relevantes.

![Dados relevantes do vs. dados irrelevantes - avaliar dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

À esquerda, a tabela apresenta o nível de álcool sangue de sete pessoas testado fora de uma barra de Boston, a média de rebatidas Red Sox no seu último jogo e o preço de milk na loja de conveniência mais próxima.

Estes são todos os dados perfeitamente legítimos. Falha só é que não é relevante. Não existe nenhuma relação óbvia entre esses números. Se alguém lhe forneceu o preço atual de milk e a média de rebatidas Red Sox, não é possível pode adivinhar seu conteúdo de álcool de sangue.

Agora, veja a tabela à direita. Desta vez, o corpo de cada pessoa em massa foi medida, bem como o número de drinks eles tem tido.  Os números em cada linha agora são relevantes para si. Se eu carreguei meu corpo em massa e o número de Margaritas tive, poderia fazer uma estimativa em meu sangue álcool conteúdo.

## <a name="do-you-have-connected-data"></a>Tiver ligado a dados?
O ingrediente seguinte é dados ligados.

![Ligado a dados vs. dados desconectados – critérios de dados, dados prontos](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Eis alguns dados relevantes na qualidade do hambúrguer: grill temperatura, peso patty e classificação de alimentar local revista. Mas tenha em atenção as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados faltam alguns valores. É comum ter buracos como esses, e existem maneiras de corrigi-las. Mas, se houver muito em falta, os seus dados começa a observar como o queijo suíço.

Se examinar a tabela no lado esquerdo, existe para que a quantidade de dados em falta, é difícil criar qualquer tipo de relação entre o peso de temperatura e patty grill. Este exemplo mostra dados desconectado.

A tabela à direita, no entanto, está cheio e conclua - um exemplo dos dados ligados.

## <a name="is-your-data-accurate"></a>São precisos os seus dados?
O ingrediente seguinte é precisão. Eis quatro destinos pressionar.

![Dados exatos vs. dados incorretos - critérios de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Examinar o destino no canto superior direito. Existe um agrupamento forte bem aproximadamente olhos bulls. Isso, é claro, é preciso. Por estranho que pareça, no idioma da ciência de dados, desempenho, à direita de destino abaixo dela também é considerado preciso.

Se mapear o Centro destas setas, verá que ele está muito próxima olhos bulls. As setas são distribuídas todo em torno de destino, para que sejam considerados imprecisa, mas eles estão centrados em torno de olho bulls, para que sejam considerados precisos.

Agora, veja o canto superior esquerdo de destino. Aqui as setas muito próximo entre si, pressione um agrupamento forte. Eles são precisos, mas são incorretas, pois o center é a forma desativar os olhos de bulls. As setas no canto inferior esquerdo destino são incorretos e imprecisa. Este archer precisa mais prática.

## <a name="do-you-have-enough-data-to-work-with"></a>Tem dados suficientes para trabalhar com?
Por fim, o ingrediente #4 é dados suficientes.

![Tem dados suficientes para análise? Avaliação de dados](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Pense em cada ponto de dados na tabela como sendo um traço de pincel num quadro. Se tiver apenas algumas delas, a pintura pode ser difusa –, é difícil dizer o que é.

Se adicionar alguns mais traços de pincel, em seguida, a pintura começa a ficar um pouco sharper.

Quando tem muito pouco suficiente traços, apenas verá suficiente para tomar algumas decisões ampla. É em algum lugar que eu deseje visitar? Parece bom, que é semelhante a água limpa – Sim, que é onde vou em férias.

À medida que adiciona mais dados, o cenário se torna mais claro e que possa tomar decisões mais detalhadas. Agora, pode ver os três hotéis em banco à esquerda. Pode observar as características da arquitetura da existente em primeiro plano. Pode até mesmo optar por manter-se no chão terceiro, devido a vista.

Com dados que são precisas e relevantes, conectada e suficiente, tem todos os ingredientes necessários para fazer alguma ciência de dados de alta qualidade.

Não se esqueça de conferir aos outros quatro vídeos *ciência de dados para principiantes* do Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Passos Seguintes
* [Tente uma primeira experimentação de ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
