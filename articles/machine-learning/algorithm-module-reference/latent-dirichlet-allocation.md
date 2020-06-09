---
title: Alocação Latente de Dirichlet
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de atribuição de Dirichlet Latent para agrupar textos não classificados em várias categorias.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: a75bf458a1c6735de42349de5d5cb6845e9ae464
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84487972"
---
# <a name="latent-dirichlet-allocation"></a>Alocação Latente de Dirichlet

Este artigo descreve como usar o módulo **de atribuição de Dirichlet Latent** no designer de aprendizagem de máquinas Azure (pré-visualização), para agrupar textos não classificados em várias categorias. 

A atribuição de dirichlet latente (LDA) é frequentemente usada no processamento de linguagem natural (NLP) para encontrar textos semelhantes. Outro termo comum é *modelação de tópicos.*

Este módulo pega numa coluna de texto e gera estas saídas:

+ O texto de origem, juntamente com uma pontuação para cada categoria

+ Uma matriz de recurso, contendo termos e coeficientes extraídos para cada categoria

+ Uma transformação, que pode guardar e reaplicar para novo texto usado como entrada

Este módulo usa a biblioteca de aprendizagem de scikit. Para obter mais informações sobre scikit-learn, consulte o repositório [GitHub, que inclui tutoriais e uma explicação do algoritmo.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Mais sobre a atribuição de Dirichlet Latent (LDA)

De um modo geral, a LDA não é um método de classificação em si, mas utiliza uma abordagem geradora. O que isto significa é que você não precisa fornecer rótulos de classe conhecidos e, em seguida, inferir os padrões.  Em vez disso, o algoritmo gera um modelo probabilístico que é usado para identificar grupos de tópicos. Pode utilizar o modelo probabilístico para classificar os casos de treino existentes ou os novos casos que fornece ao modelo como entrada.

Um modelo gerador pode ser preferível porque evita fazer suposições fortes sobre a relação entre o texto e as categorias, e usa apenas a distribuição de palavras para tópicos matematicamente modelo.

+ A teoria é discutida neste artigo, disponível como um download PDF: [Latent Dirichlet Alocação: Blei, Ng e Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ A implementação neste módulo [baseia-se na biblioteca scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) para LDA.

Para mais informações, consulte a secção [de notas técnicas.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Como configurar a atribuição de Dirichlet Latent

Este módulo requer um conjunto de dados que contenha uma coluna de texto, crua ou pré-processada.

1. Adicione o módulo **de atribuição de Dirichlet Latent** ao seu oleoduto.

2. Como entrada para o módulo, forneça um conjunto de dados contendo uma ou mais colunas de texto.

3. Para **as colunas Target,** escolha uma ou mais colunas que contenham texto para analisar.

    Pode escolher várias colunas, mas devem ser do tipo de dados de cadeia.

    Em geral, como a LDA cria uma grande matriz de recurso a partir do texto, normalmente analisa uma única coluna de texto.

4. Para **o Número de tópicos a modelar,** digite um número inteiro entre 1 e 1000 que indique quantas categorias ou tópicos pretende derivar do texto de entrada.

    Por padrão, são criados 5 tópicos.

5. No que **diz n-gramas,** especifique o comprimento máximo de N-gramas geradas durante o haxixe.

    O padrão é 2, o que significa que tanto os bigrams como os unigramas são gerados.

6. Selecione a opção **Normalizar** para converter valores de saída em probabilidades. Portanto, em vez de representar os valores transformados como número inteiros, os valores no conjunto de dados de saída e recurso seriam transformados da seguinte forma:

    + Os valores do conjunto de dados serão representados como uma probabilidade em que `P(topic|document)` .

    + Os valores na matriz de tópicos de recurso serão representados como uma probabilidade em que `P(word|topic)` .

    > [!NOTE] 
    > No designer de Machine Learning (pré-visualização), porque a biblioteca que baseamos, scikit-learn, já não suporta doc_topic_distr saída *não* normalizada da versão 0.19, portanto, neste módulo, o parâmetro **normalizar** só pode ser aplicado à saída **da matriz de Feature Topic,** a saída **de conjunto de dados transformado** está sempre normalizada.

7. Selecione a opção, **mostre todas as opções**e, em seguida, desacione-a para TRUE se quiser ver e, em seguida, definir parâmetros avançados adicionais.

    Estes parâmetros são específicos para a implementação scikit-learn da LDA. Existem alguns bons tutoriais sobre lDA no scikit-learn, bem como o documento oficial [de aprendizagem de scikit.](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)

    + **Parâmetro rho**. Fornecer uma probabilidade prévia para a esparsidade das distribuições de tópicos. Corresponde ao parâmetro de `topic_word_prior` Sklearn. Usaria o valor 1 se esperasse que a distribuição das palavras fosse plana; ou seja, todas as palavras são assumidas como equipróbíveis. Se acha que a maioria das palavras aparece escassamente, pode defini-la para um valor muito mais baixo.

    + **Parâmetro alfa.** Especifique uma probabilidade prévia para a esparsidade dos pesos tópicos por documento.  Corresponde ao parâmetro de `doc_topic_prior` Sklearn.

    + **Número estimado de documentos.** Digite um número que represente a sua melhor estimativa do número de documentos (linhas) que serão processados. Isto permite que o módulo aloque uma tabela de haxixe de tamanho suficiente.  Corresponde ao `total_samples` parâmetro em scikit-learn.

    + **Tamanho do lote.** Digite um número que indique quantas linhas incluir em cada lote de texto enviado para o modelo LDA. Corresponde ao `batch_size` parâmetro em scikit-learn.

    + **Valor inicial da iteração utilizada na programação de atualização de aprendizagem**. Especificar o valor inicial que diminui a taxa de aprendizagem para iterações precoces na aprendizagem online. Corresponde ao `learning_offset` parâmetro em scikit-learn.

    + **Potência aplicada à iteração durante as atualizações**. Indicar o nível de potência aplicado à contagem de iteração para controlar a taxa de aprendizagem durante as atualizações online. Corresponde ao `learning_decay` parâmetro em scikit-learn.

    + **Número de passes sobre os dados.** Especifique o número máximo de vezes que o algoritmo irá pedalar sobre os dados. Corresponde ao `max_iter` parâmetro em scikit-learn.

8. Selecione a opção, **Construa o dicionário de ngramas** ou **construa dicionário de ngramas antes da LDA,** se pretender criar a lista de n-gramas num passe inicial, antes de classificar o texto.

    Se criar previamente o dicionário inicial, poderá utilizar o dicionário ao rever o modelo. Ser capaz de mapear resultados para texto em vez de índices numéricos é geralmente mais fácil de interpretar. No entanto, salvar o dicionário demorará mais tempo e utilizará armazenamento adicional.

9. Para **o tamanho máximo do dicionário de ngram,** digite o número total de linhas que podem ser criadas no dicionário n-grama.

    Esta opção é útil para controlar o tamanho do dicionário. No entanto, se o número de ngramas na entrada exceder este tamanho, podem ocorrer colisões.

10. Envie o oleoduto. O módulo LDA usa o teorema de Bayes para determinar que tópicos podem estar associados a palavras individuais. As palavras não estão exclusivamente associadas a quaisquer tópicos ou grupos; em vez disso, cada n-gram tem uma probabilidade aprendida de ser associado a qualquer uma das classes descobertas.

## <a name="results"></a>Resultados

O módulo tem duas saídas:

+ **Conjunto de dados transformado**: Contém o texto de entrada e um número especificado de categorias descobertas, juntamente com as pontuações de cada exemplo de texto para cada categoria.

+ **Matriz de tópico de características**: A coluna mais à esquerda contém a função de texto extraído, e há uma coluna para cada categoria contendo a pontuação para essa característica nessa categoria.


### <a name="lda-transformation"></a>Transformação LDA

Este módulo também produz a *transformação LDA* que aplica LDA ao conjunto de dados.

Pode guardar esta transformação para outros conjuntos de dados. Isto pode ser útil se você tiver treinado em um corpus grande e quiser reutilizar os coeficientes ou categorias.
Para reutilizar esta transformação, clique no ícone do conjunto de **dados do Registo** no painel direito do módulo LDA para mantê-lo como um módulo na categoria Datasets na lista de **módulos.** Em seguida, pode ligar este módulo ao módulo [De Transformação Aplicada](apply-transformation.md) para reutilizar esta transformação.

### <a name="refining-an-lda-model-or-results"></a>Refinação de um modelo ou resultados LDA

Normalmente não é possível criar um único modelo LDA que satisfaça todas as necessidades, e mesmo um modelo projetado para uma tarefa pode requerer muitas iterações para melhorar a precisão. Recomendamos que experimente todos estes métodos para melhorar o seu modelo:

+ Alterar os parâmetros do modelo
+ Usando a visualização para entender os resultados
+ Obter o feedback dos especialistas em assuntos para verificar se os tópicos gerados são úteis.

As medidas qualitativas também podem ser úteis para avaliar os resultados. Para avaliar os resultados de modelação de tópicos, considere:

+ Precisão - Os itens similares são realmente semelhantes?
+ Diversidade - O modelo pode discriminar itens semelhantes quando necessário para o problema do negócio?
+ Escalabilidade - Funciona numa vasta gama de categorias de texto ou apenas num domínio de alvo estreito?

A precisão dos modelos baseados na LDA pode muitas vezes ser melhorada utilizando o processamento natural da linguagem para limpar, resumir e simplificar, ou categorizar texto. Por exemplo, as seguintes técnicas, todas suportadas no Azure Machine Learning, podem melhorar a precisão da classificação:

+ Parar a remoção de palavras

+ Normalização de casos

+ Lematização ou desarmamento

+ Reconhecimento de entidades nomeadas

Para obter mais informações, consulte [O Texto pré-processamento.](preprocess-text.md)

No designer, também pode utilizar bibliotecas R ou Python para processamento de texto: [Executar script R](execute-r-script.md), executar script [python](execute-python-script.md)



## <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes de implementação, dicas e respostas a perguntas frequentes.

### <a name="implementation-details"></a>Detalhes de implementação

Por padrão, as distribuições de saídas para conjunto de dados transformado e matriz de tópicos de recurso são normalizadas como probabilidades.

+ O conjunto de dados transformado é normalizado como a probabilidade condicional de tópicos dado um documento. Neste caso, a soma de cada linha é igual a 1.

+ A matriz de tópico de recurso é normalizada como a probabilidade condicional de palavras dadas a um tópico. Neste caso, a soma de cada coluna é igual a 1.

> [!TIP]
> Ocasionalmente, o módulo pode devolver um tópico vazio, que é mais frequentemente causado pela in inicialização pseudoaleatória do algoritmo.  Se isso acontecer, pode tentar alterar parâmetros relacionados, como o tamanho máximo do dicionário N-grama ou o número de bits a utilizar para o hashing da funcionalidade.

### <a name="lda-and-topic-modeling"></a>LDA e modelação de tópicos

A atribuição de Dirichlet Latent (LDA) é frequentemente utilizada para *modelação de tópicos baseados em conteúdo,* o que basicamente significa categorias de aprendizagem a partir de textos não classificados. Na modelação de tópicos baseada em conteúdo, um tópico é uma distribuição sobre palavras.

Por exemplo, assuma que forneceu um corpus de avaliações de clientes que inclui muitos, muitos produtos. O texto das avaliações que foram submetidas por muitos clientes ao longo do tempo conteria muitos termos, alguns dos quais são utilizados em vários tópicos.

Um **tópico** que seja identificado pelo processo LDA pode representar revisões para um produto A individual, ou pode representar um grupo de avaliações de produtos. Para a LDA, o tópico em si é apenas uma distribuição de probabilidade ao longo do tempo para um conjunto de palavras.

Os termos raramente são exclusivos de qualquer produto, mas podem referir-se a outros produtos, ou ser termos gerais que se aplicam a tudo ("grande", "horrível"). Outros termos podem ser palavras de ruído.  No entanto, é importante entender que o método LDA não pretende capturar todas as palavras no universo, ou entender como as palavras estão relacionadas, além de probabilidades de coocorrência. Só pode agrupar palavras que foram usadas no domínio alvo.

Após a cálculo dos índices do termo, as linhas individuais de texto são comparadas usando uma medida de semelhança baseada à distância, para determinar se duas peças de texto são iguais uma à outra.  Por exemplo, pode descobrir que o produto tem vários nomes fortemente correlacionados. Ou, pode descobrir que termos fortemente negativos estão geralmente associados a um determinado produto. Pode utilizar a medida de semelhança tanto para identificar termos relacionados como para criar recomendações.

###  <a name="module-parameters"></a>Parâmetros do módulo

|Name|Tipo|Intervalo|Opcional|Predefinição|Description|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Colunas-alvo(s)|Seleção de Colunas||Necessário|CadeiaFeature|Nome ou índice da coluna-alvo|  
|Número de tópicos para modelar|Número inteiro|[1;1000]|Necessário|5|Modelar a distribuição do documento contra os tópicos N|  
|N-gramas|Número inteiro|[1;10]|Necessário|2|Ordem de N-gramas gerada durante o haxixe|  
|Normalizar|Booleano|Verdadeiro ou Falso|Necessário|true|Normalizar a saída para as probabilidades.  O conjunto de dados transformado será P (tópico&#124;documento) e a matriz de tópico de recurso será P (tópico&#124;palavra)|  
|Mostrar todas as opções|Booleano|Verdadeiro ou Falso|Necessário|Falso|Apresenta parâmetros adicionais específicos para o scikit-learn LDA online|  
|Parâmetro rho|Float|[0.00001;1.0]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|0.01|Distribuição prévia de palavras-tópicos|  
|Parâmetro alfa|Float|[0.00001;1.0]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|0.01|Distribuição prévia de tópico de documento|  
|Número estimado de documentos|Número inteiro|[1;int. MaxValue]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|1000|Número estimado de documentos (corresponde a total_samples parâmetro)|  
|Tamanho do lote|Número inteiro|[1;1024]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|32|Tamanho do lote|  
|Valor inicial da iteração utilizado no calendário de atualização da taxa de aprendizagem|Número inteiro|[0;int. MaxValue]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|0|Valor inicial que diminui a taxa de aprendizagem para iterações precoces. Corresponde ao parâmetro learning_offset|  
|Potência aplicada à iteração durante as atualizações|Float|[0.0;1.0]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|0,5|Potência aplicada à contagem de iteração para controlar a taxa de aprendizagem. Corresponde ao parâmetro learning_decay |  
|Número de iterações de formação|Número inteiro|[1;1024]|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|25|Número de iterações de formação|  
|Construir dicionário de ngramas|Booleano|Verdadeiro ou Falso|Aplica-se quando a caixa de verificação **de todas as opções** *não* é selecionada|Verdadeiro|Constrói um dicionário de ngramas antes de computar a LDA. Útil para inspeção e interpretação de modelos|  
|Tamanho máximo do dicionário de ngram|Número inteiro|[1;int. MaxValue]|Aplica-se quando a opção **Construir dicionário de ngramas** é verdadeiro|20 000|Tamanho máximo do dicionário ngramas. Se o número de fichas na entrada exceder este tamanho, podem ocorrer colisões|  
|Número de bits a utilizar para hashing de recurso|Número inteiro|[1;31]|Aplica-se quando o **Show todas as opções** checkbox *não* é selecionada e construir dicionário **de ngramas** é falso|12|Número de bits a utilizar para hashing de recurso| 
|Construir dicionário de ngramas antes da LDA|Booleano|Verdadeiro ou Falso|Aplica-se quando o **Show todas as opções** checkbox são selecionadas|Verdadeiro|Constrói um dicionário de ngramas antes da LDA. Útil para inspeção e interpretação de modelos|  
|Número máximo de ngramas no dicionário|Número inteiro|[1;int. MaxValue]|Aplica-se quando o **Show todas as opções** checkbox é selecionada e a opção **Construir dicionário de ngramas** é verdadeiro|20 000|Tamanho máximo do dicionário. Se o número de fichas na entrada exceder este tamanho, podem ocorrer colisões|  
|Número de bits de haxixe|Número inteiro|[1;31]|Aplica-se quando o **Show todas as opções** checkbox é selecionada e a opção **Construir dicionário de ngramas** é falso|12|Número de bits a utilizar durante o hashing da funcionalidade|   


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.   
Para obter uma lista de erros específicos dos módulos, consulte [exceções e códigos de erro para o designer](designer-error-codes.md).
