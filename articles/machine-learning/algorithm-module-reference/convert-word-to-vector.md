---
title: 'Converter palavra para vetor: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar três modelos Word2Vec fornecidos para extrair um vocabulário e as suas correspondentes incorporações de palavras a partir de um corpus de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536737"
---
# <a name="convert-word-to-vector-module"></a>Converter palavra para módulo vetorial

Este artigo descreve como usar o módulo Converte word to Vetor no Azure Machine Learning designer para fazer estas tarefas:

- Aplicar vários modelos Word2Vec (Modelo Word2Vec, FastText, GloVe) no corpus de texto que especificou como entrada.
- Gere um vocabulário com incorporações de palavras.

Este módulo usa a biblioteca Gensim. Para mais informações sobre o Gensim, consulte o seu [site oficial,](https://radimrehurek.com/gensim/apiref.html)que inclui tutoriais e uma explicação de algoritmos.

### <a name="more-about-converting-words-to-vectors"></a>Mais sobre converter palavras em vetores

Converter palavras em vetores, ou vectorização de palavras, é um processo de processamento de linguagem natural (NLP). O processo usa modelos linguísticos para mapear palavras no espaço vetorial. Um espaço vetorial representa cada palavra por um vetor de números reais. Também permite que palavras com significados semelhantes tenham representações semelhantes.

Utilize incorporações de palavras como entrada inicial para tarefas a jusante de NLP, tais como classificação de texto e análise de sentimento.

Entre várias tecnologias de incorporação de palavras, neste módulo, implementámos três métodos amplamente utilizados. Dois, Word2Vec e FastText, são modelos de treino online. O outro é um modelo pré-treinado, luva-wiki-gigaword-100. 

Os modelos de treino online são treinados nos seus dados de entrada. Os modelos pré-treinados são treinados offline num corpus de texto maior (por exemplo, Wikipedia, Google News) que normalmente contém cerca de 100 mil milhões de palavras. A incorporação de palavras permanece constante durante a vectorização de palavras. Os modelos de palavras pré-treinados proporcionam benefícios como tempo de treino reduzido, melhores vetores de palavras codificados e melhor desempenho geral.

Aqui está uma informação sobre os métodos:

+ O Word2Vec é uma das técnicas mais populares para aprender incorporações de palavras usando uma rede neural rasa. A teoria é discutida neste artigo, disponível como um download PDF: [Estimativa eficiente de representações de palavras no espaço vetorial.](https://arxiv.org/pdf/1301.3781.pdf) A implementação neste módulo baseia-se na [biblioteca Gensim para o Word2Vec.](https://radimrehurek.com/gensim/models/word2vec.html)

+ A teoria FastText é explicada neste artigo, disponível como um download PDF: [Vetores de palavras enriquecedores com informação de sub-palavras.](https://arxiv.org/pdf/1607.04606.pdf) A implementação neste módulo baseia-se na [biblioteca Gensim para FastText.](https://radimrehurek.com/gensim/models/fasttext.html)

+ O modelo pré-treinado gloVe é luva-wiki-gigaword-100. É uma coleção de vetores pré-treinados baseados num corpus de texto da Wikipédia, que contém 5,6 mil milhões de fichas e 400.000 palavras de vocabulário não cobertas. Um download PDF está disponível: [GloVe: Vetores Globais para Representação de Palavras.](https://nlp.stanford.edu/pubs/glove.pdf)

## <a name="how-to-configure-convert-word-to-vector"></a>Como configurar converter palavra em vetor

Este módulo requer um conjunto de dados que contenha uma coluna de texto. Texto pré-processado é melhor.

1. Adicione o **módulo Convertendo palavra ao vetor** ao seu oleoduto.

2. Como entrada para o módulo, forneça um conjunto de dados que contenha uma ou mais colunas de texto.

3. Para **a coluna Target,** escolha apenas uma coluna que contenha texto para processar.

    Como este módulo cria um vocabulário a partir do texto, o conteúdo das colunas difere, o que leva a diferentes conteúdos vocatórios. É por isso que o módulo aceita apenas uma coluna-alvo.

4. Para  **a estratégia Word2Vec**, escolha entre **o modelo inglês pré-treinado GloVe,** **o Gensim Word2Vec**e **o Gensim FastText**.

5. Se **a estratégia word2Vec** for **Gensim Word2Vec** ou **Gensim FastText:**

    + Para **algoritmo de treino Word2Vec,** escolha entre **Skip_gram** e **CBOW**. A diferença é introduzida no [papel original (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        O método padrão é **Skip_gram**.

    + Para **o comprimento da incorporação de palavras,** especifique a dimensionalidade dos vetores de palavra. Esta definição corresponde ao `size` parâmetro em Gensim.

        O tamanho de incorporação padrão é de 100.

    + Para **o tamanho da janela de contexto,** especifique a distância máxima entre a palavra prevista e a palavra atual. Esta definição corresponde ao `window` parâmetro em Gensim.

        O tamanho da janela padrão é 5.

    + Para **o número de épocas, especifique**o número de épocas (iterações) sobre o corpus. Corresponde ao `iter` parâmetro em Gensim.

        O número de época padrão é 5.

6. Para **o tamanho máximo do vocabulário,** especifique o número máximo de palavras no vocabulário gerado.

    Se houver palavras mais únicas do que o tamanho máximo, poda as mais raras.

    O tamanho do vocabulário padrão é de 10.000.

7. Para **a contagem mínima de palavras,** forneça uma contagem mínima de palavras. O módulo ignorará todas as palavras que tenham uma frequência inferior a este valor.

    O valor predefinido é 5.

8. Envie o oleoduto.

## <a name="examples"></a>Exemplos

O módulo tem uma saída:

+ **Vocabulário com incorporações**: Contém o vocabulário gerado, juntamente com a incorporação de cada palavra. Uma dimensão ocupa uma coluna.

O exemplo a seguir mostra como funciona o módulo Converte palavra para vetor. Utiliza o Converter Palavra a Vetor com definições predefinidas para o conjunto de dados pré-processado Da Wikipédia SP 500.

### <a name="source-dataset"></a>Conjunto de dados de origem

O conjunto de dados contém uma coluna de categoria, juntamente com o texto completo recolhido da Wikipédia. A tabela que se segue mostra alguns exemplos representativos.

|Texto|
|----------|
|nasdaq 100 componente s p 500 component foundation founder location city apple campus 1 infinite loop loop loop cupertino california cupertino california cupertino california location country Estados Unidos...|
|br nasdaq 100 nasdaq 100 componente br s p 500 s p 500 s p 500 component industry computer software foundation br fundador Charles Geschke br john warnock location adobe systems...|
|s p 500 s p 500 indústria automóvel indústria automóvel antecessora da general motors corporation 1908 2009 sucessor...|
|s p 500 s p 500 componente indústria conglomerado empresa conglomerado fundação fundação fundador localização cidade fairfield connecticut connecticut connecticut local país eua área...|
|BR s p 500 s p 500 component foundation 1903 fundador William s Harley br Arthur Davidson fundador Arthur Davidson br Walter Davidson br William william a davidson localização...|

### <a name="output-vocabulary-with-embeddings"></a>Vocabulário de saída com incorporações

A tabela seguinte contém a saída deste módulo, tomando como entrada o conjunto de dados Wikipedia SP 500. A coluna mais à esquerda indica o vocabulário. O seu vetor incorporado é representado por valores das colunas restantes na mesma linha.

|Vocabulário|Incorporação dim 0|Incorporação dim 1|Incorporação dim 2|Incorporação dim 3|Incorporação dim 4|Incorporação dim 5|...|Incorporação dim 99|
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

Neste exemplo, usamos o **Gensim Word2Vec** padrão para **a estratégia Word2Vec**, e **o algoritmo de treino** é **Skip-gram**. **O comprimento da palavra Incorporação** é de 100, por isso temos 100 colunas incorporadas.

## <a name="technical-notes"></a>Notas técnicas

Esta secção contém dicas e respostas para perguntas frequentes.

+ Diferença entre o modelo de formação online e pré-treinado:

    Neste módulo Converte word to Vetor, fornecemos três estratégias diferentes: dois modelos de formação online e um modelo pré-treinado. Os modelos de formação on-line usam o seu conjunto de dados de entrada como dados de treino, e geram vetores de vocabulário e palavra durante o treino. O modelo pré-treinado já é treinado por um corpus de texto muito maior, como wikipédia ou texto do Twitter. O modelo pré-treinado é na verdade uma coleção de pares de palavras/incorporação.  

    O modelo pré-treinado GloVe resume um vocabulário a partir do conjunto de dados de entrada e gera um vetor incorporado para cada palavra a partir do modelo pré-treinado. Sem formação online, o uso de um modelo pré-treinado pode poupar tempo de treino. Tem um melhor desempenho, especialmente quando o tamanho do conjunto de dados de entrada é relativamente pequeno.

+ Tamanho de incorporação:

    Em geral, o comprimento da incorporação de palavras está definido para algumas centenas. Por exemplo, 100, 200, 300. Um pequeno tamanho incorporado significa um pequeno espaço vetorial, que pode causar colisões de incorporação de palavras.  

    O comprimento das incorporações de palavras é fixado para modelos pré-treinados. Neste exemplo, o tamanho incorporado de luva-wiki-gigaword-100 é de 100.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

Para obter uma lista de erros específicos dos módulos de design, consulte [códigos de erro de Machine Learning](designer-error-codes.md).
