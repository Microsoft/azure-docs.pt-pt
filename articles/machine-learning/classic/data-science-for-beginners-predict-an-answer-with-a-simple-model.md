---
title: 'ML Studio (clássico): Prever respostas com modelos de regressão - Azure'
description: Como criar um modelo de regressão simples para prever um preço em Data Science para Principiantes vídeo 4. Inclui uma regressão linear com dados-alvo.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c02abf095beefdd131a5835d015175d56a63de47
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322825"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo simples


## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Data Science for Beginners series
Aprenda a criar um modelo simples de regressão para prever o preço de um diamante em Data Science para principiantes vídeo 4. Vamos desenhar um modelo de regressão com dados-alvo.

Para tirar o máximo partido da série, assista a todas. [Vá à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Data Science for Beginners* é uma rápida introdução à ciência dos dados em cinco pequenos vídeos.

* Vídeo 1: [As 5 perguntas sobre as respostas da ciência dos dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: [Os seus dados estão prontos para a ciência dos dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg)*
* Vídeo 3: [Faça uma pergunta que pode responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: Prever uma resposta com um modelo simples
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcrição: Prever uma resposta com um modelo simples
Bem-vindos ao quarto vídeo da série "Data Science for Beginners". Neste, vamos construir um modelo simples e fazer uma previsão.

Um *modelo* é uma história simplificada sobre os nossos dados. Vou mostrar-lhe o que quero dizer.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Recolher dados relevantes, precisos, conectados e suficientes
Digamos que quero comprar um diamante. Tenho um anel que pertencia à minha avó com um cenário para um diamante de 1,35 quilates, e quero ter uma ideia de quanto vai custar. Levo um bloco de notas e uma caneta para a joalharia, e anoto o preço de todos os diamantes da caixa e quanto pesam em quilates. Começando com o primeiro diamante- são 1,01 quilates e $7.366.

Agora eu ando e faço isto por todos os outros diamantes da loja.

![Colunas de dados de diamantes](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Note que a nossa lista tem duas colunas. Cada coluna tem um atributo diferente - peso em quilates e preço - e cada linha é um único ponto de dados que representa um único diamante.

Na verdade, criamos um pequeno conjunto de dados aqui- uma mesa. Note que satisfaz os nossos critérios de qualidade:

* Os dados são **relevantes** - o peso está definitivamente relacionado com o preço
* É **preciso** - verificamos duas vezes os preços que escrevemos
* Está **conectado** - não há espaços em branco em nenhuma destas colunas
* E, como veremos, são dados **suficientes** para responder à nossa pergunta

## <a name="ask-a-sharp-question"></a>Faça uma pergunta afiada
Agora vamos colocar a nossa pergunta de uma forma afiada: "Quanto vai custar comprar um diamante de 1,35 quilates?"

A nossa lista não tem um diamante de 1,35 quilates, por isso vamos ter de usar o resto dos nossos dados para obter uma resposta à pergunta.

## <a name="plot-the-existing-data"></a>Traçar os dados existentes
A primeira coisa que vamos fazer é desenhar uma linha de número horizontal, chamada eixo, para traçar os pesos. A gama dos pesos é de 0 a 2, por isso vamos desenhar uma linha que cobre esse alcance e colocar carrapatos para cada meio quilate.

Em seguida, vamos desenhar um eixo vertical para registar o preço e ligá-lo ao eixo de peso horizontal. Isto será em unidades de dólares. Agora temos um conjunto de eixos de coordenadas.

![Eixos de peso e preço](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vamos pegar nestes dados agora e transformá-lo num *enredo de dispersão.* Esta é uma ótima maneira de visualizar conjuntos de dados numéricos.

Para o primeiro ponto de dados, olhamos uma linha vertical a 1,01 quilates. Depois, olhamos para uma linha horizontal a $7.366. Onde se encontram, desenhamos um ponto. Isto representa o nosso primeiro diamante.

Agora vamos ver cada diamante desta lista e fazemos a mesma coisa. Quando terminamos, isto é o que temos: um monte de pontos, um para cada diamante.

![Enredo de dispersão](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhe o modelo através dos pontos de dados
Agora, se olhares para os pontos e olhares, a coleção parece uma linha gorda e felpuda. Podemos pegar no nosso marcador e traçar uma linha reta através dele.

Ao desenhar uma linha, criámos um *modelo.* Pense nisto como tomar o mundo real e fazer uma versão simplista dos desenhos animados dele. Agora os desenhos animados estão errados- a linha não passa por todos os pontos de dados. Mas é uma simplificação útil.

![Linha linear de regressão](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

O facto de todos os pontos não passarem exatamente pela linha está bem. Os cientistas de dados explicam isto dizendo que há o modelo - que é a linha - e então cada ponto tem algum *ruído* ou *variação* associado a ele. Há a relação perfeita subjacente, e depois há o mundo real e corajoso que acrescenta ruído e incerteza.

Porque estamos a tentar responder à pergunta *Quanto* é que isto se chama *regressão.* E como estamos a usar uma linha reta, é uma *regressão linear.*

## <a name="use-the-model-to-find-the-answer"></a>Use o modelo para encontrar a resposta
Agora temos um modelo e fazemos a nossa pergunta: quanto custará um diamante de 1,35 quilates?

Para responder à nossa pergunta, olhamos 1,35 quilates e desenhamos uma linha vertical. Onde cruza a linha do modelo, olhamos uma linha horizontal para o eixo do dólar. Atinge os 10.000. Bum! Esta é a resposta: um diamante de 1,35 quilates custa cerca de $10.000.

![Encontre a resposta no modelo](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança
É natural imaginar a precisão desta previsão. É útil saber se o diamante de 1,35 quilates será muito perto de $10.000, ou muito mais ou menos. Para resolver isto, vamos desenhar um envelope em torno da linha de regressão que inclui a maioria dos pontos. Este envelope chama-se nosso intervalo de *confiança:* Estamos bastante confiantes de que os preços caem dentro deste envelope, porque no passado a maioria deles tem. Podemos desenhar mais duas linhas horizontais de onde a linha de 1,35 quilates cruza a parte superior e a parte inferior do envelope.

![Intervalo de confiança](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora podemos dizer algo sobre o nosso intervalo de confiança: podemos dizer com confiança que o preço de um diamante de 1,35 quilates é de cerca de $10.000 - mas pode ser tão baixo como $8.000 e pode chegar aos $12.000.

## <a name="were-done-with-no-math-or-computers"></a>Terminamos, sem matemática nem computadores.
Fizemos o que os cientistas de dados são pagos para fazer, e nós fizemos apenas por desenhar:

* Fizemos uma pergunta que poderíamos responder com dados
* Construímos um *modelo* usando *regressão linear*
* Fizemos uma *previsão,* completa com um intervalo de *confiança*

E não usámos matemática ou computadores para o fazer.

Agora, se tivéssemos mais informações, como...

* o corte do diamante
* variações de cor (quão perto o diamante é de ser branco)
* o número de inclusãos no diamante

... então teríamos tido mais colunas. Nesse caso, a matemática torna-se útil. Se tiver mais de duas colunas, é difícil desenhar pontos no papel. A matemática permite-lhe encaixar muito bem essa linha ou aquele avião nos seus dados.

Além disso, se em vez de apenas um punhado de diamantes, tivéssemos dois mil ou dois milhões, então podes fazer isso muito mais depressa com um computador.

Hoje, falamos sobre como fazer regressão linear, e fizemos uma previsão usando dados.

Certifique-se de conferir mais vídeos em "Data Science for Beginners" do Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente uma primeira experiência de ciência de dados com o Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](../overview-what-is-azure-ml.md)