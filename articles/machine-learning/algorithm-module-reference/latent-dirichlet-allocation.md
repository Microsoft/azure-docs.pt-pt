---
title: Atribuição de Dirichlet latente
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo latent Dirichlet Allocation para agrupar texto não classificado em várias categorias.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109228"
---
# <a name="latent-dirichlet-allocation"></a>Atribuição de Dirichlet latente

Este artigo descreve como usar o módulo **latent Dirichlet Allocation** em Azure Machine Learning designer (pré-visualização), para agrupar texto não classificado em várias categorias. 

A atribuição de dirichletla latente (LDA) é frequentemente usada no processamento de linguagem natural (NLP) para encontrar textos semelhantes. Outro termo comum é *a modelação de tópicos.*

Este módulo pega numa coluna de texto e gera estas saídas:

+ O texto de origem, juntamente com uma pontuação para cada categoria

+ Uma matriz de características, contendo termos e coeficientes extraídos para cada categoria

+ Uma transformação, que pode economizar e reaplicar a novo texto usado como entrada

Este módulo utiliza a biblioteca de aprendizagem de ficção. Para obter mais informações sobre scikit-learn, consulte o [repositório GitHub, que inclui tutoriais e uma explicação do algoritmo.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Mais sobre a atribuição de dirichlet latente (LDA)

De um modo geral, a LDA não é um método de classificação em si, mas usa uma abordagem geradora. O que isto significa é que não precisas de fornecer etiquetas de classe conhecidas e depois inferir os padrões.  Em vez disso, o algoritmo gera um modelo probabilístico que é usado para identificar grupos de tópicos. Pode utilizar o modelo probabilístico para classificar os casos de formação existentes, ou novos casos que fornece ao modelo como entrada.

Um modelo gerador pode ser preferível porque evita fazer quaisquer pressupostos fortes sobre a relação entre o texto e as categorias, e usa apenas a distribuição de palavras para tópicos matematicamente modelo.

+ A teoria é discutida neste artigo, disponível como um download pdf: [Latent Dirichlet Allocation: Blei, Ng e Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ A implementação neste módulo [baseia-se](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) na biblioteca de ficção científica para a LDA.

Para mais informações, consulte a secção [de notas Técnicas.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Como configurar a atribuição de dirichlet latente

Este módulo requer um conjunto de dados que contenha uma coluna de texto, crua ou pré-processada.

1. Adicione o módulo **latent Dirichlet Allocation** ao seu pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados contendo uma ou mais colunas de texto.

3. Para **colunas Target,** escolha uma ou mais colunas contendo texto para analisar.

    Pode escolher várias colunas, mas devem ser do tipo de dados de cadeia.

    Em geral, como a LDA cria uma grande matriz de características a partir do texto, você normalmente analisará uma única coluna de texto.

4. Para **número de tópicos a modelo**, digite um inteiro entre 1 e 1000 que indique quantas categorias ou tópicos pretende derivar do texto de entrada.

    Por padrão, são criados 5 tópicos.

5. Para **N-grams,** especifique o comprimento máximo de N-gramas gerado durante o hashing.

    O padrão é 2, o que significa que tanto os bigrams como os unigramas são gerados.

6. **Selecione** a opção Normalizar para converter valores de saída em probabilidades. Por conseguinte, em vez de representar os valores transformados como inteiros, os valores na saída e no conjunto de dados de recurso seriam transformados da seguinte forma:

    + Os valores no conjunto de dados serão representados como uma probabilidade em que `P(topic|document)`.

    + Os valores na matriz de tópicos `P(word|topic)`de recurso serão representados como uma probabilidade em que .

    > [!NOTE] 
    > No designer de Machine Learning Azure (pré-visualização), porque a biblioteca que baseamos, scikit-learn, já não suporta a saída *doc_topic_distr* não normalizada a partir da versão 0.19, portanto, neste módulo, o parâmetro **normalizar** só pode ser aplicado à saída da **matriz tópico de recurso,** a saída de conjunto de **dados transformado** está sempre normalizada.

7. Selecione a opção, **mostre todas as opções**e, em seguida, defina-a para TRUE se quiser ver e, em seguida, definir parâmetros avançados adicionais.

    Estes parâmetros são específicos da implementação de Ficção Científica da LDA. Existem alguns bons tutoriais sobre LDA no scikit-learn, bem como o documento oficial [de ficção científica.](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)

    + **Parâmetro de ro.** Fornecer uma probabilidade prévia para a esparsidade das distribuições de tópicos. Corresponde ao parâmetro do `topic_word_prior` SKlearn. Utilizaria o valor 1 se espera que a distribuição de palavras seja plana; ou seja, todas as palavras são assumidas equiprobable. Se acha que a maioria das palavras parece escassamente, pode defini-la para um valor muito mais baixo.

    + **Parâmetro alfa.** Especifique uma probabilidade prévia para a esparsidade dos pesos tópicos por documento.  Corresponde ao parâmetro do `doc_topic_prior` SKlearn.

    + **Número estimado de documentos.** Digite um número que represente a sua melhor estimativa do número de documentos (linhas) que serão processados. Isto permite que o módulo aloque uma tabela de hash de tamanho suficiente.  Corresponde ao `total_samples` parâmetro em scikit-learn.

    + **Tamanho do lote.** Digite um número que indique quantas linhas incluir em cada lote de texto enviado para o modelo LDA. Corresponde ao `batch_size` parâmetro em scikit-learn.

    + **Valor inicial da iteração utilizada no calendário de atualização de aprendizagem.** Especifique o valor inicial que diminui a taxa de aprendizagem para iterações precoces na aprendizagem online. Corresponde ao `learning_offset` parâmetro em scikit-learn.

    + **Potência aplicada à iteração durante atualizações**. Indicar o nível de potência aplicado à contagem de iterações para controlar a taxa de aprendizagem durante as atualizações online. Corresponde ao `learning_decay` parâmetro em scikit-learn.

    + **Número de passes sobre os dados.** Especifique o número máximo de vezes que o algoritmo irá pedalar sobre os dados. Corresponde ao `max_iter` parâmetro em scikit-learn.

8. Selecione a opção, construa dicionário **de ngrams** ou **construa dicionário de ngrams antes de LDA,** se quiser criar a lista n-grama num passe inicial, antes de classificar o texto.

    Se criar previamente o dicionário inicial, poderá utilizar o dicionário ao rever o modelo. Ser capaz de mapear resultados para texto em vez de índices numéricos é geralmente mais fácil para interpretação. No entanto, salvar o dicionário levará mais tempo e utilizará armazenamento adicional.

9. Para o **tamanho máximo do dicionário ngram,** digite o número total de linhas que podem ser criadas no dicionário n-grama.

    Esta opção é útil para controlar o tamanho do dicionário. Todavia, se o número de ngramas na entrada exceder este tamanho, podem ocorrer colisões.

10. Submeta o oleoduto. O módulo LDA usa teorema de Bayes para determinar que tópicos podem estar associados a palavras individuais. As palavras não estão exclusivamente associadas a quaisquer tópicos ou grupos; em vez disso, cada n-grama tem uma probabilidade aprendida de ser associado a qualquer uma das classes descobertas.

## <a name="results"></a>Resultados

O módulo tem duas saídas:

+ **Conjunto de dados transformado**: Contém o texto de entrada e um número especificado de categorias descobertas, juntamente com as pontuações para cada exemplo de texto para cada categoria.

+ **Matriz**de tópicos de recurso : A coluna mais à esquerda contém a função de texto extraída, e há uma coluna para cada categoria que contém a pontuação dessa característica nessa categoria.


### <a name="lda-transformation"></a>Transformação lDA

Este módulo também produz a *transformação lDA* que aplica LDA ao conjunto de dados.

Pode guardar esta transformação através do registo do conjunto de dados sob o separador **Outputs+logs** no painel direito do módulo e reutilizá-lo para outros conjuntos de dados. Isto pode ser útil se tiver treinado num corpus grande e quiser reutilizar os coeficientes ou categorias.

### <a name="refining-an-lda-model-or-results"></a>Refinação de um modelo ou resultados lDA

Normalmente não se pode criar um único modelo LDA que irá satisfazer todas as necessidades, e mesmo um modelo projetado para uma tarefa pode exigir muitas iterações para melhorar a precisão. Recomendamos que experimente todos estes métodos para melhorar o seu modelo:

+ Alteração dos parâmetros do modelo
+ Usando a visualização para entender os resultados
+ Obter o feedback dos especialistas em assuntos para verificar se os tópicos gerados são úteis.

As medidas qualitativas também podem ser úteis para avaliar os resultados. Para avaliar os resultados da modelação de tópicos, considere:

+ Precisão - Itens semelhantes são realmente similares?
+ Diversidade - Pode o modelo discriminar entre itens semelhantes quando necessário para o problema do negócio?
+ Escalabilidade - Funciona numa vasta gama de categorias de texto ou apenas num domínio alvo estreito?

A precisão dos modelos baseados na LDA pode muitas vezes ser melhorada utilizando o processamento de linguagem natural para limpar, resumir e simplificar, ou categorizar texto. Por exemplo, as seguintes técnicas, todas suportadas no Azure Machine Learning, podem melhorar a precisão de classificação:

+ Parar a remoção de palavras

+ Normalização de casos

+ Lemtização ou stemming

+ Reconhecimento de entidades nomeadas

Para mais informações, consulte [Texto pré-processo](preprocess-text.md).

No designer, também pode utilizar bibliotecas R ou Python para processamento de texto: [Execute R Script](execute-r-script.md), Execute O Script [Python](execute-python-script.md)



## <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="implementation-details"></a>Detalhes da implementação

Por padrão, as distribuições de saídas para conjunto de dados transformado e matriz de tópicos de características são normalizadas como probabilidades.

+ O conjunto de dados transformado é normalizado como a probabilidade condicional de tópicos dados de um documento. Neste caso, a soma de cada linha é igual a 1.

+ A matriz tópico de recurso é normalizada como a probabilidade condicional de palavras dadas a um tópico. Neste caso, a soma de cada coluna é igual a 1.

> [!TIP]
> Ocasionalmente, o módulo pode devolver um tópico vazio, que é mais frequentemente causado pela pseudoaleatória inicialização do algoritmo.  Se isso acontecer, pode tentar alterar parâmetros relacionados, como o tamanho máximo do dicionário N-gram ou o número de bits a utilizar para o hashing de características.

### <a name="lda-and-topic-modeling"></a>LDA e modelação de tópicos

A atribuição de dirichletla latente (LDA) é frequentemente usada para *modelação de tópicos baseados em conteúdo*, o que basicamente significa categorias de aprendizagem de texto não classificado. Na modelação de tópicos baseados em conteúdo, um tópico é uma distribuição sobre palavras.

Por exemplo, assuma que forneceu um corpus de avaliações de clientes que inclui muitos, muitos produtos. O texto de comentários que foram submetidos por muitos clientes ao longo do tempo conteria muitos termos, alguns dos quais são usados em vários tópicos.

Um **tópico** identificado pelo processo LDA pode representar revisões para um produto aindividualdes, ou pode representar um grupo de avaliações de produtos. Para a LDA, o tema em si é apenas uma distribuição de probabilidadeao longo do tempo para um conjunto de palavras.

Os termos raramente são exclusivos de qualquer produto, mas podem referir-se a outros produtos, ou ser termos gerais que se aplicam a tudo ("grande", "horrível"). Outros termos podem ser palavras de barulho.  No entanto, é importante entender que o método LDA não pretende capturar todas as palavras no universo, ou entender como as palavras estão relacionadas, além das probabilidades de coocorrência. Só pode agrupar palavras que foram usadas no domínio alvo.

Após a computação dos índices de termo, as linhas individuais de texto são comparadas utilizando uma medida de semelhança baseada na distância, para determinar se duas peças de texto são iguais umas às outras.  Por exemplo, pode descobrir que o produto tem vários nomes que estão fortemente correlacionados. Ou, pode descobrir que termos fortemente negativos estão geralmente associados a um determinado produto. Pode usar a medida de semelhança tanto para identificar termos relacionados como para criar recomendações.

###  <a name="module-parameters"></a>Parâmetros do módulo

|Nome|Tipo|Intervalo|Opcional|Predefinição|Descrição|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Colunas-alvo(s)|Seleção de Colunas||Necessário|StringFeature|Nome ou índice da coluna de destino|  
|Número de tópicos para modelo|Número inteiro|[1;1000]|Necessário|5|Modelar a distribuição de documentos contra tópicos N|  
|N-gramas|Número inteiro|[1;10]|Necessário|2|Ordem de N-gramas gerada durante o hashing|  
|Normalizar|Booleano|Verdadeiro ou Falso|Necessário|true|Normalizar a saída para probabilidades.  O conjunto de dados transformado será P (tópico&#124;documento) e a matriz do tópico de recurso será P (palavra&#124;tópico)|  
|Mostrar todas as opções|Booleano|Verdadeiro ou Falso|Necessário|Falso|Apresenta parâmetros adicionais específicos para scikit-learn online LDA|  
|Parâmetro rho|Float|[0.00001;1.0]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|0.01|Palavra de tópico anterior distribuição|  
|Parâmetro alfa|Float|[0.00001;1.0]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|0.01|Documento de distribuição prévia|  
|Número estimado de documentos|Número inteiro|[1;int. MaxValue]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|1000|Número estimado de documentos (corresponde a total_samples parâmetro)|  
|Tamanho do lote|Número inteiro|[1;1024]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|32|Tamanho do lote|  
|Valor inicial da iteração usada no calendário de atualização da taxa de aprendizagem|Número inteiro|[0;int. MaxValue]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|0|Valor inicial que diminui a taxa de aprendizagem para iterações precoces. Corresponde ao parâmetro learning_offset|  
|Potência aplicada à iteração durante atualizações|Float|[0.0;1.0]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|0,5|Poder aplicado à contagem de iterações para controlar a taxa de aprendizagem. Corresponde ao parâmetro learning_decay |  
|Número de iterações de treino|Número inteiro|[1;1024]|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|25|Número de iterações de treino|  
|Construir dicionário de ngrams|Booleano|Verdadeiro ou Falso|Aplica-se quando a caixa de verificação **show todas as opções** *não* são selecionadas|Verdadeiro|Constrói um dicionário de ngrams antes de calcular a LDA. Útil para inspeção e interpretação de modelos|  
|Tamanho máximo do dicionário ngram|Número inteiro|[1;int. MaxValue]|Aplica-se quando a opção **Construir dicionário de ngrams** é verdadeiro|20 000|Tamanho máximo do dicionário de ngrams. Se o número de fichas na entrada exceder este tamanho, podem ocorrer colisões|  
|Número de bits para usar para hashing de recurso|Número inteiro|[1;31]|Aplica-se quando o **Show todas as opções** *de* checkbox não são selecionadas e construir dicionário **de ngrams** é falso|12|Número de bits para usar para hashing de recurso| 
|Construir dicionário de ngrams antes da LDA|Booleano|Verdadeiro ou Falso|Aplica-se quando a caixa de verificação **show todas as opções** são selecionadas|Verdadeiro|Constrói um dicionário de ngrams antes da LDA. Útil para inspeção e interpretação de modelos|  
|Número máximo de ngrams no dicionário|Número inteiro|[1;int. MaxValue]|Aplica-se quando o **Show todas as opções** de checkbox são selecionadas e a opção **Construir dicionário de ngrams** é verdadeiro|20 000|Tamanho máximo do dicionário. Se o número de fichas na entrada exceder este tamanho, podem ocorrer colisões|  
|Número de pedaços de hash|Número inteiro|[1;31]|Aplica-se quando o **Show todas as opções** de checkbox são selecionadas e a opção **Construir dicionário de ngrams** é Falso|12|Número de bits para usar durante o hashing de recurso|   


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.   
Para obter uma lista de erros específicos dos módulos, consulte [Exceções e códigos](designer-error-codes.md)de erro para o designer .
