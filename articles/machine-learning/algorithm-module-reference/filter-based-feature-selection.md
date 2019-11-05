---
title: 'Seleção de recursos baseada em filtro: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo seleção de recursos baseada em filtro no serviço Azure Machine Learning para identificar os recursos em um conjunto de informações com o maior poder de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: ac1421c93f1a4ca42d7f1d94bb898c423c380a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517972"
---
# <a name="filter-based-feature-selection"></a>Seleção de recursos baseada em filtro

Este artigo descreve como usar o módulo [seleção de recursos baseada em filtro](filter-based-feature-selection.md) no designer de Azure Machine Learning (versão prévia), para identificar as colunas em seu conjunto de dados de entrada que têm a maior capacidade de previsão. 

Em geral, a *seleção de recursos* refere-se ao processo de aplicação de testes estatísticos a entradas, dadas uma saída especificada, para determinar quais colunas são mais previsíveis na saída. O módulo [seleção de recursos baseada em filtro](filter-based-feature-selection.md) fornece vários algoritmos de seleção de recursos para escolher, incluindo métodos de correlação, como os valores de correlação Pearson e qui-quadrado. 

Ao usar o módulo [seleção de recursos baseada em filtro](filter-based-feature-selection.md) , você fornece um conjunto de um, identifica a coluna que contém o rótulo ou a variável dependente e, em seguida, especifica um único método a ser usado na medição da importância do recurso.

O módulo gera um conjunto de resultados que contém as melhores colunas de recursos, conforme classificado por energia preditiva. Ele também gera os nomes dos recursos e suas pontuações da métrica selecionada.  

### <a name="what-is-filter-based-feature-selection-and-why-use-it"></a>O que é a seleção de recursos baseada em filtro e por que usá-lo?  

Esse módulo para seleção de recursos é chamado de "baseado em filtro" porque você usa a métrica selecionada para identificar atributos irrelevantes e filtra colunas redundantes do seu modelo.  Você escolhe uma medida estatística única que atenda aos seus dados e o módulo calcula uma pontuação para cada coluna de recurso. As colunas são retornadas classificadas por suas pontuações de recursos. 

Ao escolher os recursos certos, é possível melhorar a precisão e a eficiência da classificação. 

Normalmente, você usa apenas as colunas com as melhores pontuações para criar seu modelo de previsão. Colunas com pontuações de seleção de recursos insatisfatórios podem ser deixadas no conjunto de e ignoradas quando você cria um modelo.  

### <a name="how-to-choose-a-feature-selection-metric"></a>Como escolher uma métrica de seleção de recursos

A **seleção de recursos baseada em filtro** fornece uma variedade de métricas para avaliar o valor das informações em cada coluna.  Esta seção fornece uma descrição geral de cada métrica e como ela é aplicada. Os requisitos adicionais para usar cada métrica são indicados na seção [notas técnicas](#technical-notes) e nas [instruções](#how-to-configure-filter-based-feature-selection) para configurar cada módulo.

-   **Correlação de Pearson**  

     A estatística de correlação de Pearson ou o coeficiente de correlação de Pearson também é conhecido em modelos estatísticos como o valor de `r`. Para quaisquer duas variáveis, ele retorna um valor que indica a força da correlação

     O coeficiente de correlação de Pearson é calculado por meio da covariância de duas variáveis e da divisão pelo produto de seus desvios padrão. O coeficiente não é afetado pelas alterações de escala nas duas variáveis.  

-   **Qui-quadrado**  

     O teste qui-quadrado de duas vias é um método estatístico que mede como os valores de fechamento esperados são para resultados reais. O método supõe que as variáveis sejam aleatórias e desenhadas a partir de um exemplo adequado de variáveis independentes. A estatística qui-quadrada resultante indica a distância em que os resultados são do resultado esperado (aleatório).  


> [!TIP]
> Se você precisar de uma opção diferente para o método de seleção de recurso personalizado, use o módulo [Executar script R](execute-r-script.md) . 
##  <a name="how-to-configure-filter-based-feature-selection"></a>Como configurar a seleção de recursos com base em filtro

Você escolhe uma métrica estatística padrão e o módulo computa a correlação entre um par de colunas: a coluna de rótulo e uma coluna de recursos

1.  Adicione o módulo **seleção de recursos baseada em filtro** ao seu pipeline. Você pode encontrá-lo na categoria **seleção de recursos** no designer.

2. Conecte um conjunto de dados de entrada que contenha pelo menos duas colunas que são recursos potenciais.  

    Para garantir que uma coluna seja analisada e uma pontuação de recurso gerada, use o módulo [Editar metadados](edit-metadata.md) para definir o atributo **isfeature** . 

    > [!IMPORTANT]
    > Verifique se as colunas que você está fornecendo como entrada são recursos potenciais. Por exemplo, uma coluna que contém um único valor não tem nenhum valor de informação.
    >
    > Se você souber que há colunas que tornaram recursos inválidos, poderá removê-las da seleção de coluna. Você também pode usar o módulo [Editar metadados](edit-metadata.md) para sinalizá-los como **categóricos**. 
3.  Para o **método de Pontuação de recurso**, escolha um dos métodos estatísticos estabelecidos a seguir para usar no cálculo de pontuações.  

    | Método              | Requisitos                             |
    | ------------------- | ---------------------------------------- |
    | Correlação de Pearson | O rótulo pode ser texto ou numérico. Os recursos devem ser numéricos. |
    Qui-quadrado| Os rótulos e os recursos podem ser texto ou numéricos. Use esse método para calcular a importância do recurso para duas colunas categóricas.|

    > [!TIP]
    > Se você alterar a métrica selecionada, todas as outras seleções serão redefinidas, portanto, certifique-se de definir esta opção primeiro!)
4.  Selecione a opção **operar apenas em colunas de recurso** para gerar uma pontuação somente para as colunas que foram previamente marcadas como recursos. 

    Se você desmarcar essa opção, o módulo criará uma pontuação para qualquer coluna que atenda aos critérios, até o número de colunas especificado no **número de recursos desejados**.  

5.  Para **coluna de destino**, clique em **Iniciar seletor de coluna** para escolher a coluna de rótulo por nome ou por seu índice (os índices são baseados em um).  

     Uma coluna de rótulo é necessária para todos os métodos que envolvem correlação estatística. O módulo retornará um erro de tempo de design se você não escolher nenhuma coluna de rótulo ou várias colunas de rótulo. 

6.  Para o **número de recursos desejados**, digite o número de colunas de recursos que você deseja retornar como resultado.  

     - O número mínimo de recursos que você pode especificar é 1, mas recomendamos que você aumente esse valor.  

     - Se o número especificado de recursos desejados for maior que o número de colunas no conjunto de resultados, todos os recursos serão retornados, mesmo aqueles com pontuação zero.  

    - Se você especificar menos colunas de resultado do que as colunas de recurso, os recursos serão classificados por Pontuação decrescente e somente os principais recursos serão retornados. 

7.  Execute o pipeline ou selecione o módulo [seleção de recursos com base em filtro](filter-based-feature-selection.md) e clique em **executar selecionado**.


## <a name="results"></a>Resultados

Após a conclusão do processamento:

+ Para ver uma lista completa das colunas de recursos que foram analisadas e suas pontuações, clique com o botão direito do mouse no módulo, selecione **recursos**e clique em **Visualizar**.  

+ Para exibir o conjunto de recursos que é gerado com base nos critérios de seleção de recurso, clique com o botão direito do mouse no módulo, selecione **conjunto**de um e clique em **Visualizar**. 

Se o conjunto de dados contiver menos colunas do que o esperado, verifique as configurações do módulo e os tipos de dado das colunas fornecidas como entrada. Por exemplo, se você definir o **número de recursos desejados** como 1, o conjunto de resultados de saída conterá apenas duas colunas: a coluna de rótulo e a coluna de recurso com mais alta classificação.


##  <a name="technical-notes"></a>Notas técnicas  

### <a name="implementation-details"></a>Detalhes da implementação

Se você usar a correlação de Pearson em um recurso numérico e um rótulo categórico, a Pontuação do recurso será calculada da seguinte maneira:  

1.  Para cada nível na coluna categórica, calcule a média condicional da coluna numérica.  

2.  Correlacione a coluna de média condicional com a coluna numérica.  

### <a name="requirements"></a>Requisitos  

-   Uma classificação de seleção de recurso não pode ser gerada para nenhuma coluna designada como um **rótulo** ou como uma coluna de **Pontuação** .  

-   Se você tentar usar um método de pontuação com uma coluna de um tipo de dados sem suporte pelo método, o módulo gerará um erro ou uma pontuação zero será atribuída à coluna.  

-   Se uma coluna contiver valores lógicos (true/false), eles serão processados como true = 1 e false = 0.  

-   Uma coluna não poderá ser um recurso se tiver sido designada como um **rótulo** ou uma **Pontuação**.  

### <a name="how-missing-values-are-handled"></a>Como os valores ausentes são tratados  

-   Você não pode especificar como uma coluna de destino (rótulo) qualquer coluna que tenha todos os valores ausentes.  

-   Se uma coluna contiver valores ausentes, eles serão ignorados ao computar a pontuação da coluna.  

-   Se uma coluna designada como uma coluna de recurso tiver todos os valores ausentes, uma pontuação de zero será atribuída.   


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 

