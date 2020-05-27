---
title: Converter palavra em vetor
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar três modelos Word2Vec fornecidos para extrair um vocabulário e a sua palavra correspondente incorpora-se a partir de um corpus de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853772"
---
# <a name="convert-word-to-vector"></a>Converter palavra em vetor

Este artigo descreve como usar a **palavra Convert para** o módulo Vetor em Azure Machine Learning designer (Preview), para aplicar vários modelos Word2Vec (Word2Vec, FastText, Glove modelo pré-treinado) no corpus do texto que especificou como entrada, e gerar um vocabulário com incorporações de palavras.

Este módulo utiliza a biblioteca Gensim. Para mais informações sobre gensim, consulte o seu [site oficial](https://radimrehurek.com/gensim/apiref.html) que inclui tutoriais e explicação de algoritmos.

### <a name="more-about-convert-word-to-vector"></a>Mais sobre converter palavra para vetor

Em termos gerais, converter palavra em vetor, ou vetorização de palavras, é um processo de processamento de linguagem natural, que usa modelos ou técnicas de linguagem para mapear palavras em espaço vetorial, isto é, para representar cada palavra por um vetor de números reais, e entretanto, permite que palavras com significados semelhantes tenham representações semelhantes.

As incorporações de palavras podem ser usadas como entrada inicial para tarefas a jusante do NLP, tais como classificação de texto, análise de sentimentos, etc.

Entre várias tecnologias de incorporação de palavras, neste módulo, implementamos três métodos amplamente utilizados, incluindo dois modelos de formação online, Word2Vec e FastText, e um modelo pré-treinado, luva-wiki-gigaword-100. Os modelos de treino online são treinados nos seus dados de entrada, enquanto os modelos pré-treinados são treinados off-line em um corpus de texto maior, (por exemplo, Wikipedia, Google News) geralmente contém cerca de 100 mil milhões de palavras, então, a incorporação de palavras permanece constante durante a vetorização de palavras. Os modelos de palavrapré-treinados pré-treinados proporcionam benefícios como tempo de treino reduzido, melhores vetores de palavras codificados e melhor desempenho geral.

+ O Word2Vec é uma das técnicas mais populares para aprender incorporações de palavras usando rede neural rasa, a teoria é discutida neste artigo, disponível como um download pdf: [Estimativa eficiente de representações de palavras no espaço vetorial, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). A implementação neste módulo [baseia-se na biblioteca gensim para o Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ A teoria fastText é explicada neste artigo, disponível como um download pdf: [Enriquecendo vetores de palavras com informação de sub-palavra, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). A implementação neste módulo [baseia-se na biblioteca gensim para fastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Modelo pré-treinado de luva: glove-wiki-gigaword-100, é uma coleção de vetores pré-treinados baseados no corpo de texto da Wikipédia, que contém fichas de 5.6B e vocabulário sem caso de 400K, pdf está disponível: [GloVe: Vetores globais para representação](https://nlp.stanford.edu/pubs/glove.pdf)de palavras.

## <a name="how-to-configure-convert-word-to-vector"></a>Como configurar a conversão da palavra para o vetor

Este módulo requer um conjunto de dados que contenha uma coluna de texto, texto pré-processado é melhor.

1. Adicione a **Palavra Convertida ao** módulo vetor ao seu pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados contendo uma ou mais colunas de texto.

3. Para a **coluna Target,** escolha apenas uma coluna contendo texto para processar.

    Em geral, como este módulo cria um vocabulário a partir de texto, o conteúdo de diferentes colunas difere, o que leva a diferentes conteúdos de vocabulário, portanto, o módulo só aceita uma coluna-alvo.

4. Para **a estratégia Word2Vec,** escolha `GloVe pretrained English Model` entre, e `Gensim Word2Vec` `Gensim FastText` .

5. se a **estratégia Word2Vec** for `Gensim Word2Vec` `Gensim FastText` ou:

    + Algoritmo de **treino Word2Vec.** Escolha de `Skip_gram` e `CBOW` . A diferença é introduzida no [papel](https://arxiv.org/pdf/1301.3781.pdf)original.

        O método predefinido é `Skip_gram` .

    + **Comprimento da palavra incorporação**. Especifique a dimensionalidade dos vetores da palavra. Corresponde ao `size` parâmetro em gensim.

        O embedding_size padrão é de 100.

    + **Tamanho da janela de contexto.** Especifique a distância máxima entre a palavra que está a ser prevista e a palavra atual. Corresponde ao `window` parâmetro em gensim.

        O tamanho da janela padrão é 5.

    + **Número de épocas.** Especifique o número de épocas (iterações) sobre o corpus. Corresponde ao `iter` parâmetro em gensim.

        O número de épocas padrão é 5.

6. Para o tamanho máximo do **vocabulário,** especifique o número máximo das palavras no vocabulário gerado.

    Se há palavras mais únicas do que esta, então ameixa as raras.

    O tamanho do vocabulário padrão é 10000.

7. Para a contagem mínima de **palavras,** forneça uma contagem mínima de palavras, o que faz com que o módulo ignore todas as palavras, que têm uma frequência inferior a este valor.

    O valor padrão é 5.

8. Submeta o oleoduto.

## <a name="examples"></a>Exemplos

O módulo tem uma saída:

+ **Vocabulário com incorporações**: Contém o vocabulário gerado, juntamente com a incorporação de cada palavra, uma dimensão ocupa uma coluna.

### <a name="result-examples"></a>Exemplos de resultados

Para ilustrar como funciona o módulo **Convert Word para vetor,** o exemplo seguinte aplica este módulo com as definições predefinidas para o conjunto de dados Wikipédia SP 500 pré-processado fornecido em Azure Machine Learning (pré-visualização).

#### <a name="source-dataset"></a>Conjunto de dados de origem

O conjunto de dados contém uma coluna de categoria, bem como o texto completo recolhido da Wikipédia. Esta tabela apresenta apenas alguns exemplos representativos.

|texto|
|----------|
|Nasdaq 100 componente s p 500 componente fundação fundador localização city apple campus 1 infinite loop loop street infinite loop cupertino california cupertino california local país Estados Unidos...|
|br nasdaq 100 nasdaq 100 componente br s p 500 s p 500 component industry computer software foundation br founder Charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 indústria automóvel indústria automóvel antecessor general motors corporation 1908 2009 sucessor...|
|s p 500 s p 500 componente indústria conglomerado empresa conglomerado fundação fundador localização cidade fairfield connecticut connecticut connecticut localização país eua área...|
|br s p 500 s p 500 component foundation 1903 fundador William s harley br arthur Davidson harley Davidson fundador Arthur Davidson br walter davidson br william a davidson localização...|

#### <a name="output-vocabulary-with-embeddings"></a>Vocabulário de saída com incorporações

A tabela seguinte contém a saída deste módulo tomando o conjunto de dados Wikipedia SP 500 como entrada. A coluna mais à esquerda indica o vocabulário, o seu vetor de incorporação é representado por valores de colunas restantes na mesma linha.

|Vocabulário|Incorporar dim 0|Incorporar dim 1|Incorporar dim 2|Incorporar dim 3|Incorporar dim 4|Incorporar dim 5|...|Incorporar dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|componente|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|t|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
fundação|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
fundador|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
localização|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
maçã|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinito|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

Neste exemplo, usamos o padrão `Gensim Word2Vec` como a **estratégia Word2Vec**, Algoritmo de **Treino** é , Comprimento da `Skip-gram` palavra **Incorporação** é 100, portanto, temos 100 colunas de incorporação.

## <a name="technical-notes"></a>Notas técnicas

Esta secção contém dicas e respostas a perguntas frequentes.

+ Diferença entre o comboio online e o modelo pré-treinado

    Nesta **palavra Converte para módulo Vetor,** fornecemos três estratégias diferentes, dois modelos de formação online e um modelo pré-treinado. O modelo de treino online usa o seu conjunto de dados de entrada como dados de treino, gera vocabulário e vetores de palavras durante o treino, enquanto o modelo pré-treinado já é treinado por um corpus de texto muito maior, como o texto wikipédia ou o twitter, pelo que o modelo pré-treinado é na verdade uma coleção de pares (palavra, incorporação).  

    Se o modelo pré-treinado de Glove for escolhido como estratégia de vectorização de palavras, resume um vocabulário a partir do conjunto de dados de entrada e gera um vetor de incorporação para cada palavra do modelo pré-treinado, sem treino online, o uso de modelo pré-treinado poderia economizar tempo de treino, e tem um melhor desempenho especialmente quando o tamanho do conjunto de dados de entrada é relativamente pequeno.

+ Tamanho de incorporação

    Em geral, o comprimento da incorporação de palavras é definido para algumas centenas (por exemplo, 100, 200, 300) para alcançar um bom desempenho, porque pequeno tamanho de incorporação significa pequeno espaço vetorial, o que pode causar colisões de incorporação de palavras.  

    Para modelos pré-treinados, o comprimento das incorporações de palavras são fixos, nesta implementação, incorporar o tamanho da luva-wiki-gigaword-100 é 100.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

Para obter uma lista de erros específicos dos módulos de design (pré-visualização), consulte os códigos de erro de [aprendizagem automática](designer-error-codes.md).
