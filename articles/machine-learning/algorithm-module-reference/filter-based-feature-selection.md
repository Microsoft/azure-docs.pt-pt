---
title: 'Seleção de funcionalidades baseada no filtro: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de seleção de funcionalidades baseado em filtros no Azure Machine Learning para identificar as funcionalidades num conjunto de dados com a maior potência preditiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477600"
---
# <a name="filter-based-feature-selection"></a>Seleção de funcionalidades baseadas em filtro

Este artigo descreve como utilizar o módulo de seleção de funcionalidades baseado no filtro no designer de machine learning Azure (pré-visualização). Este módulo ajuda-o a identificar as colunas no seu conjunto de dados de entrada que têm a maior potência preditiva. 

Em geral, a *seleção* de recursos refere-se ao processo de aplicação de testes estatísticos a inputs, dada uma saída especificada. O objetivo é determinar quais as colunas mais preditivas da saída. O módulo de seleção de funcionalidades baseado no filtro fornece vários algoritmos de seleção de funcionalidades para escolher. O módulo inclui métodos de correlação como a correlação pearson e os valores cin-quadrados. 

Quando utilizar o módulo de seleção de funcionalidades baseado no filtro, fornece um conjunto de dados e identifica a coluna que contém o rótulo ou a variável dependente. Em seguida, especifique um único método para utilizar na medição da importância da característica.

O módulo produz um conjunto de dados que contém as melhores colunas de funcionalidades, classificadas por potência preditiva. Também produz os nomes das funcionalidades e as suas pontuações a partir da métrica selecionada.  

## <a name="what-filter-based-feature-selection-is"></a>Qual é a seleção de funcionalidades baseada em filtros  

Este módulo para seleção de funcionalidades chama-se "baseado em filtros" porque utiliza a métrica selecionada para encontrar atributos irrelevantes. Em seguida, filtra-se colunas redundantes do seu modelo. Você escolhe uma única medida estatística que se adequa aos seus dados, e o módulo calcula uma pontuação para cada coluna de funcionalidades. As colunas são devolvidas classificadas pelas suas pontuações de recurso. 

Ao escolher as funcionalidades certas, pode potencialmente melhorar a precisão e eficiência da classificação. 

Normalmente, usa apenas as colunas com as melhores pontuações para construir o seu modelo preditivo. Colunas com pontuações de seleção de funcionalidades fracas podem ser deixadas no conjunto de dados e ignoradas quando se constrói um modelo.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Como escolher uma métrica de seleção de recursos

O módulo de seleção de funcionalidades baseado no filtro fornece uma variedade de métricas para avaliar o valor da informação em cada coluna. Esta secção fornece uma descrição geral de cada métrica, e como é aplicada. Pode encontrar requisitos adicionais para a utilização de cada métrica nas [notas técnicas](#technical-notes) e nas [instruções](#how-to-configure-filter-based-feature-selection) para configurar cada módulo.

-   **Correlação pearson**  

    A estatística da correlação de Pearson, ou o coeficiente de `r` correlação de Pearson, também é conhecida nos modelos estatísticos como o valor. Para qualquer duas variáveis, devolve um valor que indica a força da correlação.

    O coeficiente de correlação de Pearson é calculado tomando a covariance de duas variáveis e dividindo-se pelo produto dos seus desvios padrão. As mudanças de escala nas duas variáveis não afetam o coeficiente.  

-   **Chi ao quadrado**  

    O teste de duas vias é um método estatístico que mede a proximidade dos valores esperados aos resultados reais. O método pressupõe que as variáveis são aleatórias e retiradas de uma amostra adequada de variáveis independentes. A estatística em linha de chi-quadrado resultante indica até que ponto os resultados estão do resultado esperado (aleatório).  


> [!TIP]
> Se precisar de uma opção diferente para o método de seleção de funcionalidades personalizadas, utilize o módulo [Execute R Script.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Como configurar a seleção de funcionalidades baseada no filtro

Escolhe-se uma métrica estatística padrão. O módulo calcula a correlação entre um par de colunas: a coluna de etiquetas e uma coluna de características.

1.  Adicione o módulo de seleção de funcionalidades baseado no filtro ao seu pipeline. Pode encontrá-lo na categoria **De Seleção** de Recursos no designer.

2. Ligue um conjunto de dados de entrada que contenha pelo menos duas colunas que são potenciais características.  

    Para garantir que uma coluna é analisada e gera uma pontuação de funcionalidade, utilize o módulo [Dedados editar](edit-metadata.md) para definir o atributo **IsFeature.** 

    > [!IMPORTANT]
    > Certifique-se de que as colunas que está a fornecer como entrada são potenciais características. Por exemplo, uma coluna que contém um único valor não tem valor de informação.
    >
    > Se souber que algumas colunas fariam más características, pode removê-las da seleção da coluna. Também pode utilizar o módulo [de Metadados editar](edit-metadata.md) para os sinalizar como **Categóricos.** 
3.  Para o método de **pontuação feature,** escolha um dos seguintes métodos estatísticos estabelecidos para utilizar no cálculo das pontuações.  

    | Método              | Requisitos                             |
    | ------------------- | ---------------------------------------- |
    | Correlação pearson | A etiqueta pode ser texto ou numérico. As características devem ser numéricas. |
    Chi ao quadrado| Etiquetas e características podem ser texto ou numérico. Utilize este método para a computação característica de importância para duas colunas categóricas.|

    > [!TIP]
    > Se alterar a métrica selecionada, todas as outras seleções serão redefinidas. Por isso, certifique-se de definir esta opção primeiro.
4.  Selecione o **opção de operar nas colunas de características apenas** para gerar uma pontuação apenas para colunas que foram previamente marcadas como funcionalidades. 

    Se limpar esta opção, o módulo criará uma pontuação para qualquer coluna que, de outro modo, satisfaça os critérios, até ao número de colunas especificadas no **Número de Características desejadas**.  

5.  Para **a coluna Target,** selecione seletor de **colunas de lançamento** para escolher a coluna de etiquetas, quer pelo nome quer pelo seu índice. (Os índices são de base única.)  
    É necessária uma coluna de etiquetas para todos os métodos que envolvam correlação estatística. O módulo devolve um erro de tempo de conceção se não escolher nenhuma coluna de etiqueta ou colunas de etiquetamúltiplas. 

6.  Para **o número de funcionalidades desejadas,** introduza o número de colunas de funcionalidades que pretende devolver como resultado:  

    - O número mínimo de funcionalidades que pode especificar é um, mas recomendamos que aumente este valor.  

    - Se o número especificado de características desejadas for maior do que o número de colunas no conjunto de dados, então todas as funcionalidades são devolvidas. Até as características com zero pontuações são devolvidas.  

    - Se especificar menos colunas de resultados do que as colunas de características, as características são classificadas por pontuação descendente. Apenas as principais características são devolvidas. 

7.  Envie o pipeline ou selecione o módulo de seleção de funcionalidades baseado no filtro e, em seguida, selecione **Executar selecionado**.


## <a name="results"></a>Resultados

Após o processamento estar concluído:

+ Para ver uma lista completa das colunas de funcionalidades analisadas e das suas pontuações, clique no módulo e **selecione Visualize**.  

+ Para visualizar o conjunto de dados com base nos critérios de seleção das suas funcionalidades, clique no módulo e **selecione Visualize**. 

Se o conjunto de dados contiver menos colunas do que o esperado, verifique as definições do módulo. Verifique também os tipos de dados das colunas fornecidas como entrada. Por exemplo, se definir **o número de funcionalidades desejadas** para 1, o conjunto de dados de saída contém apenas duas colunas: a coluna de etiquetas e a coluna de características mais bem classificada.


##  <a name="technical-notes"></a>Notas técnicas  

### <a name="implementation-details"></a>Detalhes da implementação

Se utilizar a correlação pearson numa característica numérica e numa etiqueta categórica, a pontuação da funcionalidade é calculada da seguinte forma:  

1.  Para cada nível na coluna categórica, calcule a média condicional da coluna numérica.  

2.  Correlacionar a coluna de meios condicionais com a coluna numérica.  

### <a name="requirements"></a>Requisitos  

-   Uma pontuação de seleção de recursos não pode ser gerada para qualquer coluna que seja designada como uma coluna **de Etiqueta** ou **Pontuação.**  

-   Se tentar utilizar um método de pontuação com uma coluna de um tipo de dados que o método não suporta, o módulo irá levantar um erro. Ou, uma pontuação zero será atribuída à coluna.  

-   Se uma coluna contiver valores lógicos (verdadeiros/falsos), são processados como `True = 1` e `False = 0`.  

-   Uma coluna não pode ser uma característica se tiver sido designada como **Etiqueta** ou **Pontuação**.  

### <a name="how-missing-values-are-handled"></a>Como os valores em falta são tratados  

-   Não é possível especificar como coluna de alvo (etiqueta) qualquer coluna que tenha todos os valores em falta.  

-   Se uma coluna contiver valores em falta, o módulo ignora-os quando está a calcular a pontuação para a coluna.  

-   Se uma coluna designada como coluna de características tiver todos os valores em falta, o módulo atribui uma pontuação zero.   


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

