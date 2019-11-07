---
title: Prever respostas com modelos de regressão
titleSuffix: ML Studio (classic) Azure
description: Como criar um modelo de regressão simples para prever um preço no vídeo ciência de dados para iniciantes 4. Inclui uma regressão linear com dados de destino.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 2148938323ed3f819f14be88590723da40462453
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619525"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo simples
## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: série de ciência de dados para iniciantes
Saiba como criar um modelo de regressão simples para prever o preço de um losango no vídeo de ciência de dados para iniciantes 4. Vamos desenhar um modelo de regressão com dados de destino.

Para obter o máximo da série, Assista a todos eles. [Ir para a lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
A *ciência de dados para iniciantes* é uma rápida introdução à ciência de dados em cinco vídeos curtos.

* Vídeo 1: [as cinco perguntas que a ciência de dados responde](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Vídeo 2: [seus dados estão prontos para a ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Vídeo 3: [faça uma pergunta que você possa responder com os dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Vídeo 4: prever uma resposta com um modelo simples
* Vídeo 5: [copiar o trabalho de outras pessoas para a ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 minutos e 18 segundos)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcrição: prever uma resposta com um modelo simples
Bem-vindo ao quarto vídeo da série "ciência de dados para iniciantes". Neste um, vamos criar um modelo simples e fazer uma previsão.

Um *modelo* é uma história simplificada sobre nossos dados. Mostrarei o que eu quero dizer.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Colete dados relevantes, precisos, conectados e suficientes
Digamos que eu queira comprar um losango. Tenho um anel que pertencia ao meu avó com uma configuração para um diamante de 1,35 quilate e quero ter uma ideia de quanto ele custará. Pegarei um bloco de notas e uma caneta na joia Store, e escrevo o preço de todos os ouros no caso e quanto eles pesam em quilate. A partir do primeiro diamante, é 1, 1 quilate e $7366.

Agora, vou fazer isso para todos os outros ouros na loja.

![Colunas de dados de losango](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observe que nossa lista tem duas colunas. Cada coluna tem um peso de atributo diferente em quilate e Price-e cada linha é um único ponto de dados que representa um único losango.

Na verdade, criamos um pequeno conjunto de dados aqui-uma tabela. Observe que ele atende aos nossos critérios de qualidade:

* Os dados são **relevantes** para que o peso esteja definitivamente relacionado ao preço
* É preciso que tenhamos verificado **cuidadosamente** os preços que escrevemos
* Ele está **conectado** -não há espaços em branco em nenhuma dessas colunas
* E, como veremos, são dados **suficientes** para responder à nossa pergunta

## <a name="ask-a-sharp-question"></a>Faça uma pergunta nítida
Agora, apresentaremos nossa pergunta de uma maneira nítida: "quanto custará comprar um diamante de 1,35 quilate?"

Nossa lista não tem um quilate Diamond de 1,35, portanto, precisaremos usar o restante de nossos dados para obter uma resposta para a pergunta.

## <a name="plot-the-existing-data"></a>Plotar os dados existentes
A primeira coisa que faremos é desenhar uma linha de número horizontal, chamada de eixo, para criar um gráfico dos pesos. O intervalo dos pesos é de 0 a 2, portanto, vamos desenhar uma linha que cubra esse intervalo e colocar tiques para cada metade de quilate.

Em seguida, vamos desenhar um eixo vertical para registrar o preço e conectá-lo ao eixo de espessura horizontal. Isso estará em unidades de dólar. Agora temos um conjunto de eixos de coordenadas.

![Eixos de peso e preço](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vamos pegar esses dados agora e transformá-los em um gráfico de *dispersão*. Essa é uma ótima maneira de visualizar conjuntos de dados numéricos.

Para o primeiro ponto de dados, olho uma linha vertical em 1, 1 quilate. Em seguida, olho uma linha horizontal em $7366. Onde eles se encontram, desenhamos um ponto. Isso representa nosso primeiro diamante.

Agora, passamos por cada diamante nessa lista e fazemos a mesma coisa. Quando estamos usando, isso é o que obtemos: vários pontos, um para cada diamante.

![Gráfico de dispersão](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhar o modelo por meio dos pontos de dados
Agora, se você olhar os pontos e semicerrar, a coleção se parece com uma linha de FAT, difusa. Podemos pegar nosso marcador e desenhar uma linha reta.

Ao desenhar uma linha, criamos um *modelo*. Considere isso como pegar o mundo real e criar uma versão de desenho simplista. Agora o desenho está errado – a linha não passa por todos os pontos de dados. Mas, é uma simplificação útil.

![Linha de regressão linear](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

O fato de que todos os pontos não passam exatamente pela linha está OK. Os cientistas de dados explicam isso dizendo que há o modelo, que é a linha e, em seguida, cada ponto tem algum *ruído* ou *variação* associada a ele. Há a relação perfeita subjacente e, em seguida, existe o mundo real que adiciona ruído e incerteza.

Porque estamos tentando responder à pergunta o *quanto?* isso é chamado de *regressão*. E como estamos usando uma linha reta, é uma *regressão linear*.

## <a name="use-the-model-to-find-the-answer"></a>Usar o modelo para localizar a resposta
Agora temos um modelo e fazemos nossa pergunta: quanto custará 1,35 quilate Diamond?

Para responder à nossa pergunta, nós olhomos 1,35 quilate e desenhamos uma linha vertical. Onde ele cruza a linha do modelo, nós olhomos uma linha horizontal ao eixo de dólar. Chega à direita em 10.000. Pronto! Essa é a resposta: um quilate de 1,35 em diamante custa cerca de $10000.

![Localizar a resposta no modelo](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança
É natural imaginar qual é a precisão dessa previsão. É útil saber se o 1,35 quilate Diamond será muito próximo de $10000 ou muito mais ou menos. Para descobrir isso, vamos desenhar um envelope em torno da linha de regressão que inclui a maioria dos pontos. Esse envelope é chamado de nosso *intervalo de confiança*: estamos muito confiantes de que os preços se enquadram nesse envelope, pois, na última parte, eles têm. Podemos desenhar duas linhas horizontais de onde a linha 1,35 quilate cruza a parte superior e a inferior do envelope.

![Intervalo de confiança](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora, podemos dizer algo sobre nosso intervalo de confiança: podemos dizer com confiança que o preço de um diamante de 1,35 quilate é cerca de $10000, mas pode ser tão baixo quanto $8000 e pode ser tão alto quanto $12000.

## <a name="were-done-with-no-math-or-computers"></a>Pronto, sem matemática ou computadores
Fizemos o que os cientistas de dados são pagos, e fizemos isso apenas por meio de desenho:

* Pedimos uma pergunta que poderíamos responder com os dados
* Criamos um *modelo* usando a *regressão linear*
* Fizemos uma *previsão*, completo com um intervalo de *confiança*

E não usamos cálculos ou computadores para fazer isso.

Agora, se tivéssemos mais informações, como...

* o recorte do losango
* variações de cores (o quão perto o diamante está sendo branco)
* o número de inclusões no losango

... em seguida, teríamos mais colunas. Nesse caso, Math se torna útil. Se você tiver mais de duas colunas, é difícil desenhar pontos no papel. A matemática permite que você ajuste a linha ou o plano aos seus dados muito bem.

Além disso, se, em vez de apenas alguns ouros, tivéssemos 2000 ou 2 milhões, você poderá fazer isso funcionar muito mais rapidamente com um computador.

Hoje, falamos sobre como fazer uma regressão linear e fizemos uma previsão usando dados.

Certifique-se de conferir os outros vídeos em "ciência de dados para iniciantes" em Microsoft Azure Machine Learning Studio (clássico).

## <a name="next-steps"></a>Passos seguintes
* [Experimente um primeiro experimento de ciência de dados com Machine Learning Studio (clássico)](create-experiment.md)
* [Obtenha uma introdução a Machine Learning em Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
