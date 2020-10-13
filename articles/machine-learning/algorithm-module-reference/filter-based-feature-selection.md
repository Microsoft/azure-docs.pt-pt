---
title: 'Seleção de recursos baseados em filtro: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de seleção de recursos baseado em filtros no Azure Machine Learning para identificar as funcionalidades num conjunto de dados com a maior potência preditiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945707"
---
# <a name="filter-based-feature-selection"></a>Seleção de funcionalidades baseadas em filtro

Este artigo descreve como utilizar o módulo de seleção de recursos baseado em filtro no designer de aprendizagem automática Azure. Este módulo ajuda-o a identificar as colunas do conjunto de dados de entrada que têm a maior potência preditiva. 

Em geral, *a seleção* de recursos refere-se ao processo de aplicação de testes estatísticos às entradas, dada uma saída especificada. O objetivo é determinar quais as colunas mais preditivas da saída. O módulo de seleção de recursos baseado em filtros fornece vários algoritmos de seleção de funcionalidades para escolher. O módulo inclui métodos de correlação como a correlação de Pearson e os valores chi-quadrados. 

Quando utiliza o módulo de seleção de funções baseado em filtro, fornece um conjunto de dados e identifica a coluna que contém o rótulo ou a variável dependente. Em seguida, especifique um único método para utilizar na medição da importância da função.

O módulo produz um conjunto de dados que contém as melhores colunas de recurso, classificadas pela potência preditiva. Também produz os nomes das funcionalidades e as suas pontuações a partir da métrica selecionada.  

## <a name="what-filter-based-feature-selection-is"></a>Qual é a seleção de recursos baseada em filtros  

Este módulo para a seleção de funcionalidades chama-se "baseado em filtros" porque utiliza a métrica selecionada para encontrar atributos irrelevantes. Em seguida, filtra colunas redundantes do seu modelo. Você escolhe uma única medida estatística que se adequa aos seus dados, e o módulo calcula uma pontuação para cada coluna de recurso. As colunas são devolvidas classificadas pelas suas pontuações de recurso. 

Ao escolher as características certas, pode potencialmente melhorar a precisão e eficiência da classificação. 

Normalmente, usa-se apenas as colunas com as melhores pontuações para construir o seu modelo preditivo. Colunas com pontuações de seleção de recursos deficientes podem ser deixadas no conjunto de dados e ignoradas quando se constrói um modelo.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Como escolher uma métrica de seleção de recursos

O módulo de seleção de recursos Filter-Based fornece uma variedade de métricas para avaliar o valor da informação em cada coluna. Esta secção fornece uma descrição geral de cada métrica, e como é aplicada. Pode encontrar requisitos adicionais para a utilização de cada métrica nas [notas técnicas](#technical-notes) e nas [instruções](#how-to-configure-filter-based-feature-selection) para configurar cada módulo.

-   **Correlação pearson**  

    A estatística de correlação de Pearson, ou coeficiente de correlação de Pearson, também é conhecida nos modelos estatísticos como o `r` valor. Para qualquer duas variáveis, devolve um valor que indica a força da correlação.

    O coeficiente de correlação de Pearson é calculado tomando a covariância de duas variáveis e dividindo-se pelo produto dos seus desvios padrão. As mudanças de escala nas duas variáveis não afetam o coeficiente.  

-   **Chi ao quadrado**  

    O teste chi-quadrado de duas vias é um método estatístico que mede a proximidade dos valores esperados aos resultados reais. O método pressupõe que as variáveis são aleatórias e extraídas de uma amostra adequada de variáveis independentes. A estatística chi-quadrada resultante indica a distância dos resultados do resultado esperado (aleatório).  


> [!TIP]
> Se precisar de uma opção diferente para o método de seleção de funcionalidades personalizadas, utilize o módulo [Executar Script R.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Como configurar a seleção de recursos Filter-Based

Escolhe-se uma métrica estatística padrão. O módulo calcula a correlação entre um par de colunas: a coluna de etiqueta e uma coluna de características.

1.  Adicione o módulo de seleção de recursos Filter-Based ao seu oleoduto. Pode encontrá-lo na categoria **de Seleção de Recursos** no designer.

2. Conecte um conjunto de dados de entrada que contenha pelo menos duas colunas que são funcionalidades potenciais.  

    Para garantir que uma coluna é analisada e uma pontuação de funcionalidade é gerada, utilize o módulo [editar metadados](edit-metadata.md) para definir o atributo **IsFeature.** 

    > [!IMPORTANT]
    > Certifique-se de que as colunas que está a fornecer como entrada são características potenciais. Por exemplo, uma coluna que contenha um único valor não tem valor de informação.
    >
    > Se souber que algumas colunas fariam más características, pode removê-las da seleção da coluna. Também pode utilizar o módulo [editar metadados](edit-metadata.md) para os sinalizar como **Categorical.** 
3.  Para **o método de pontuação de recurso,** escolha um dos seguintes métodos estatísticos estabelecidos para utilizar no cálculo das pontuações.  

    | Método              | Requisitos                             |
    | ------------------- | ---------------------------------------- |
    | Correlação pearson | A etiqueta pode ser texto ou numérico. As características devem ser numéricas. |
    Chi ao quadrado| As etiquetas e funcionalidades podem ser texto ou numérico. Utilize este método para calcular a importância do recurso para duas colunas categóricas.|

    > [!TIP]
    > Se alterar a métrica selecionada, todas as outras seleções serão reiniciadas. Então certifique-se de definir esta opção primeiro.
4.  Selecione a opção **'Operar' apenas nas colunas de funcionalidades** para gerar uma pontuação apenas para colunas que foram previamente marcadas como funcionalidades. 

    Se limpar esta opção, o módulo criará uma pontuação para qualquer coluna que, de outra forma, satisfaça os critérios, até ao número de colunas especificadas no **Número de características desejadas**.  

5.  Para **a coluna Target**, selecione seletor de **colunas de lançamento** para escolher a coluna de etiquetas pelo nome ou pelo seu índice. (Os índices são baseados em uma base.)  
    É necessária uma coluna de etiquetas para todos os métodos que envolvam correlação estatística. O módulo devolve um erro de tempo de conceção se não escolher nenhuma coluna de etiqueta ou várias colunas de etiquetas. 

6.  Para **o número de funcionalidades desejadas,** insira o número de colunas de funcionalidades que deseja devolvidas como resultado:  

    - O número mínimo de funcionalidades que pode especificar é um, mas recomendamos que aumente este valor.  

    - Se o número especificado de características desejadas for maior do que o número de colunas no conjunto de dados, então todas as funcionalidades são devolvidas. Até funcionalidades com zero pontuações são devolvidas.  

    - Se especificar menos colunas de resultados do que as colunas de características, as características são classificadas por pontuação descendente. Apenas as características superiores são devolvidas. 

7.  Envie o oleoduto.

> [!IMPORTANT]
> Se vai utilizar a **Seleção de Recursos Baseados** em Filtros em inferência, tem de utilizar [o Select Columns Transform](./select-columns-transform.md) para armazenar o resultado selecionado e aplicar a [Transformação](./apply-transformation.md) para aplicar a transformação selecionada para o conjunto de dados de pontuação.
>
> Consulte a seguinte imagem para construir o seu pipeline, para garantir que as seleções de colunas são as mesmas para o processo de pontuação.
> [!div class="mx-imgBorder"]
> ![Gasoduto de amostra](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Results

Após o processamento estar concluído:

+ Para ver uma lista completa das colunas de funcionalidades analisadas e as suas pontuações, clique com o botão direito no módulo e **selecione Visualize**.  

+ Para ver o conjunto de dados com base nos critérios de seleção de funcionalidades, clique no módulo e **selecione Visualize**. 

Se o conjunto de dados contiver menos colunas do que o esperado, verifique as definições do módulo. Verifique também os tipos de dados das colunas fornecidas como entrada. Por exemplo, se definir **Número de características desejadas** para 1, o conjunto de dados de saída contém apenas duas colunas: a coluna de etiquetas e a coluna de características mais bem classificada.


##  <a name="technical-notes"></a>Notas técnicas  

### <a name="implementation-details"></a>Detalhes de implementação

Se utilizar a correlação Pearson numa característica numérica e num rótulo categórico, a pontuação de recurso é calculada da seguinte forma:  

1.  Para cada nível na coluna categórica, calcule a média condicional da coluna numérica.  

2.  Correlacionar a coluna dos meios condicional com a coluna numérica.  

### <a name="requirements"></a>Requisitos  

-   Uma pontuação de seleção de recursos não pode ser gerada para qualquer coluna que seja designada como uma coluna **label** ou **score.**  

-   Se tentar utilizar um método de pontuação com uma coluna de um tipo de dado que o método não suporta, o módulo levantará um erro. Ou, uma pontuação zero será atribuída à coluna.  

-   Se uma coluna contiver valores lógicos (verdadeiros/falsos), são processados como `True = 1` e `False = 0` .  

-   Uma coluna não pode ser uma característica se tiver sido designada como **Etiqueta** ou **Pontuação**.  

### <a name="how-missing-values-are-handled"></a>Como os valores em falta são tratados  

-   Não é possível especificar como coluna alvo (etiqueta) qualquer coluna que tenha todos os valores em falta.  

-   Se uma coluna contiver valores em falta, o módulo ignora-os quando está a calcular a pontuação para a coluna.  

-   Se uma coluna designada como coluna de recurso tiver todos os valores em falta, o módulo atribui uma pontuação zero.   


## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

