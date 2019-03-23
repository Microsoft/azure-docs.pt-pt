---
title: Prever as respostas com modelos de regressão
titleSuffix: Azure Machine Learning Studio
description: Como criar um modelo de regressão simples para prever um preço em ciência de dados para principiantes, vídeo 4. Inclui uma regressão linear com dados de destino.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 9165e51d07cf97756408c7f73720931abe067bb2
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371759"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo simples
## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Ciência de dados para a série de iniciantes
Saiba como criar um modelo de regressão simples para prever o preço de um diamante em ciência de dados para principiantes, vídeo 4. Vamos desenhar um modelo de regressão com dados de destino.

Para obter o máximo proveito da série, assista a todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos desta série
*Ciência de dados para principiantes* é uma introdução rápida à ciência dos dados em cinco vídeos curtos.

* Vídeo 1: [Respostas de ciência de dados de questões do 5](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg)*
* Vídeo 2: [Seus dados está pronto para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg)*
* Vídeo 3: [Faça uma pergunta que pode ser respondidas com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg)*
* Vídeo 4: Prever uma resposta com um modelo simples
* Vídeo 5: [Copiar o trabalho de outras pessoas para fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcrição: Prever uma resposta com um modelo simples
Bem-vindo ao vídeo nos "dados ciência para principiantes" quarto série. Este aqui, vamos criar um modelo simple e efetuar uma predição.

R *modelo* é uma história simplificada sobre nossos dados. Vou mostrar o que quero dizer.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Recolher relevante, precisa, conectado, dados suficientes
Digamos que eu queira shop para um diamante. Tenho um anel que pertencia a com uma definição para um diamante acento circunflexo 1.35 do meu script e quero ter uma idéia de quanto vai custar. Pego um bloco de notas e caneta para o arquivo de joias e o anota o preço de todos os de ouros no caso e quanto eles fazem suas ponderações carats. Começando com a primeira diamond - 1.01 carats e US $7,366.

Agora percorrer e fazê-lo para todos os outros ouros na loja.

![Colunas de dados de diamante](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Tenha em atenção que a nossa lista tem duas colunas. Cada coluna tem um atributo diferente - peso em carats e preço - e cada linha é um único ponto de dados que representa um diamante único.

Na verdade, criamos um pequeno conjunto de dados aqui – uma tabela. Tenha em atenção que cumpre nossos critérios de qualidade:

* Os dados são **relevantes** -peso definitivamente está relacionado ao preço
* Ele possui **precisos** -podemos com verificação dupla os preços que escrevemos para baixo
* Ele possui **ligado** -não existem sem espaços em branco em qualquer uma destas colunas
* E, como verá, ele possui **suficiente** dados para responder a nossa pergunta

## <a name="ask-a-sharp-question"></a>Faça uma pergunta sharp
Agora, vai fazer o nosso pergunta de uma forma de sustenido: "Quanto ele custará comprar um diamante acento circunflexo 1.35?"

Nossa lista não tem um acento circunflexo 1.35 diamante, então teremos que usar o resto dos nossos dados para obter uma resposta à pergunta.

## <a name="plot-the-existing-data"></a>Os dados existentes de desenho
A primeira coisa que vamos fazer é desenhar uma linha horizontal de número, chamada de um eixo, para os pesos de gráfico. O intervalo dos pesos é de 0 a 2, pelo que vamos desenhar uma linha que abrange que vão e colocar as marcações para cada acento circunflexo metade.

Em seguida vamos desenhar um eixo vertical para registar o preço e ligá-la para o eixo horizontal peso. Esta será em unidades de dólares. Agora temos um conjunto de coordenadas eixos.

![Eixos de peso e o preço](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vamos pegar esses dados agora e torná-la uma *dispersão*. Esta é uma excelente forma de visualizar os conjuntos de dados numéricos.

Para o primeiro ponto de dados, podemos eyeball uma linha vertical no 1.01 carats. Em seguida, podemos eyeball uma linha horizontal em US $7,366. Sempre que cumprem, preste um ponto. Isso representa o nosso primeiro losango.

Agora vamos passar por cada losango nesta lista e fazer a mesma coisa. Quando passamos pelo, este é o que obtemos: uma série de pontos, um para cada losango.

![Dispersão](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhar o modelo através dos pontos de dados
Agora se examinar os pontos e squint, a coleção é semelhante a uma linha fat, difusa. Podemos fazer nossa marcador e desenhar uma linha reta através do mesmo.

Ao desenhar uma linha, criamos um *modelo*. Pense nisso como pegar o mundo real e fazer uma animado simplista de versão do mesmo. Agora o animado está errado – a linha não passar por todos os pontos de dados. No entanto, é uma simplificação útil.

![Linha de regressão linear](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

O fato de que todos os pontos não passam exatamente através da linha está OK. Os cientistas de dados explicar esta questão selecionando o que é o modelo – o que é a linha - e, em seguida, cada ponto tem algumas *ruído* ou *variância* associados a ele. Existe a relação perfeita subjacente e, em seguida, há do mundo real, que adiciona o ruído e a incerteza.

Uma vez que estamos tentando responder à pergunta *quanto?* isso é chamado um *regressão*. E porque estamos usando uma linha reta, é um *regressão linear*.

## <a name="use-the-model-to-find-the-answer"></a>Usar o modelo para encontrar a resposta
Agora temos um modelo e podemos fazer nossa pergunta: Quanto custará o losango um acento circunflexo 1.35?

Para responder a nossa pergunta, podemos olho 1.35 carats e desenhar uma linha vertical. Onde ele cruza a linha de modelo, podemos eyeball uma linha horizontal para o eixo de dólar. Chegar a direita em 10 000. Boom! Essa é a resposta: Custos de diamante um acento circunflexo 1.35 sobre $10.000.

![Encontrar a resposta no modelo](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança
É natural para se pergunte como precisa é desta predição. É útil para saber se o acento circunflexo 1.35 losango estará muito perto de $10.000, ou muito superior ou inferior. Para isso, vamos desenhar um envelope em torno da linha de regressão que inclui a maioria dos pontos. Este envelope é chamado nosso *intervalo de confiança*: Estamos bastante confiança de que os preços coincidir com este envelope, porque as últimos maioria deles têm. Podemos pode desenhar duas linhas horizontais mais de onde a linha de acento circunflexo 1.35 cruza na parte superior e a parte inferior do que envelope.

![Intervalo de confiança](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora, podemos dizer algo sobre o nosso intervalo de confiança:  Podemos dizer com confiança que o preço de um acento circunflexo 1.35 losango é aproximadamente US $10.000 - mas pode ser apenas US $8.000 e pode ser tão elevada como 12 000 $.

## <a name="were-done-with-no-math-or-computers"></a>Pronto, sem matemática ou computadores
Dissemos que os cientistas de dados sejam pagas para o fazer. e fizemos desenhando apenas:

* Podemos fez uma pergunta é poderia responder com dados
* Criámos uma *modelo* usando *regressão linear*
* Fizemos uma *predição*, completa com uma *intervalo de confiança*

E não usamos aulas de matemática ou computadores para fazê-lo.

Agora se tinha tivéssemos obter mais informações, como...

* o corte do losango
* variações de cores (como fechar o losango é que está a ser branco)
* o número de inclusões no losango

.... Then teríamos que mais colunas. Nesse caso, matemática se torna útil. Se tiver mais de duas colunas, é difícil desenhar pontos no papel. A matemática permite-lhe são perfeitas para essa linha ou esse plano aos seus dados.

Além disso, se, em vez de apenas um punhado de ouros, tivemos dois mil ou dois milhões, em seguida, pode fazer esse trabalho muito mais rapidamente com um computador.

Hoje, já falamos sobre como fazer a regressão linear e fizemos uma predição ao utilizar os dados.

Certifique-se de que consulta outros vídeos em "Dados ciência para principiantes" do Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Passos Seguintes
* [Tente uma primeira experimentação de ciência de dados com o Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
