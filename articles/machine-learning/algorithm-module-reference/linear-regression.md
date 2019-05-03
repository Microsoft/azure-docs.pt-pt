---
title: 'Regressão linear: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de regressão Linear no serviço Azure Machine Learning para criar um modelo de regressão linear para utilização numa experimentação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029719"
---
# <a name="linear-regression-module"></a>Módulo de regressão linear
Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo de regressão linear para utilização numa experimentação.  Regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico ou variável dependente. 

Utilize este módulo para definir um método de regressão linear e, em seguida, preparar um modelo com um conjunto de dados etiquetado. O modelo preparado, em seguida, pode ser utilizado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

Regressão linear é um método de estatístico comuns, que foi adotado no machine learning e aprimorado com muitos métodos novos para que se ajusta a linha e medir erro. No sentido de forma mais básico, regressão refere-se a predição de um destino numérico. Regressão linear ainda é uma boa opção quando pretender que um modelo simples para uma tarefa de previsão básica. Regressão linear também tende a funcionar bem em conjuntos de dados altamente dimensionais, dispersos na falta de complexidade.

O Azure Machine Learning suporta uma variedade de modelos de regressão, além de regressão linear. No entanto, o termo "regression" pode ser interpretado livremente, e alguns tipos de regressão fornecido noutras ferramentas não são suportados.

+ O problema de regressão clássico envolve uma única variável independente e uma variável dependente. Isso é chamado *regressão simple*.  Este módulo suporta regressão simple.

+ *Regressão linear vários* envolve dois ou mais variáveis independentes que contribuem para uma única variável dependente. Também são chamados de problemas em que várias entradas são utilizadas para prever um único resultado numérico *regressão linear multivariada*.

    O **regressão Linear** módulo pode resolver estes problemas, como a maioria dos outros módulos de regressão.

+ *Regressão de etiqueta Multi* é a tarefa de várias variáveis dependentes dentro de um modelo único de prever. Por exemplo, na etiqueta multi de regressão logística, um exemplo pode ser atribuído a várias etiquetas diferentes. (Isto é diferente da tarefa de prever vários níveis dentro de uma variável de classe única.)

    Este tipo de regressão não é suportado no Azure Machine Learning. Para prever as diversas variáveis, crie um aprendiz separado para cada resultado que pretende prever.

Durante anos estatísticos têm desenvolvido métodos de cada vez mais avançados para regressão. Isso é verdade mesmo para regressão linear. Este módulo suporta dois métodos para medir o erro e ajustar a linha de regressão: método de quadrados de menos comum e gradiente descendente.

- **Gradiente descendente** é um método que minimiza a quantidade de erro em cada etapa do processo de treinamento de modelo. Há muitas variações no gradiente descendente e a otimização para vários problemas de aprendizado tem sido muito estudada. Se escolher esta opção para **método de solução**, pode definir uma variedade de parâmetros para controlar o tamanho de etapa, taxa de aprendizagem e assim por diante. Esta opção também suporta a utilização de um varrimentos integrada.

- **Quadrados do menos comuns** é uma das técnicas mais usadas em regressão linear. Por exemplo, menos quadrados é o método que é utilizado nas a análise para Microsoft Excel.

    Quadrados do menos comuns refere-se para a função de perda, que calcula o erro como a soma do quadrado de distância do valor real para a linha prevista e, se encaixa o modelo ao minimizarem o erro ao quadrado. Este método assume uma forte relação linear entre as entradas e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar a regressão Linear

Este módulo suporta dois métodos para que se ajusta a um modelo de regressão, com opções diferentes:

+ [Criar um modelo de regressão online descendente de gradação a utilizar](#bkmk_GradientDescent)

    Gradiente descendente é uma função de perda melhor para modelos que são mais complexas ou que tenham dados de treinamento muito pouco, dadas o número de variáveis.



+ [Ajustar um modelo de regressão com comuns de menos de quadrados](#bkmk_OrdinaryLeastSquares)

    Para conjuntos de dados pequenas, é melhor selecionar comuns de menos de quadrados. Isso deve lhe dar resultados semelhantes para o Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Criar um modelo de regressão com comuns de menos de quadrados

1. Adicionar a **modelo de regressão Linear** módulo à sua experimentação na interface.

    Pode encontrar este módulo na **Machine Learning** categoria. Expanda **inicializar modelo**, expanda **regressão**e, em seguida, arraste o **modelo de regressão Linear** módulo à sua experimentação.

2. Na **propriedades** painel, na **método de solução** lista pendente, selecione **comum de menos de quadrados**. Esta opção especifica o método de cálculo utilizado para localizar a linha de regressão.

3. Na **peso de regularização de L2**, digite o valor para utilizar como o peso de regularização de L2. Recomendamos que utilize um valor diferente de zero para evitar overfitting.

     Para saber mais sobre como regularização afeta o ajuste de modelo, consulte este artigo: [L1 e L2 regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecione a opção **termo de intercepção de inclusão**, se pretender ver o termo para a interceptação.

    Desmarcar esta opção se não precisar de rever a fórmula de regressão.

5. Para **seed número aleatório**, opcionalmente, pode escrever um valor para efetuar o seeding do gerador de números aleatórios usado pelo modelo.

    Usando um valor de semente é útil se pretender manter os mesmos resultados em diferentes execuções da experimentação mesmo. Caso contrário, a predefinição é usar um valor entre o relógio do sistema.


7. Adicionar a [Train Model](./train-model.md) módulo à sua experimentação e ligue-se de um conjunto de dados etiquetado.

8. Execute a experimentação.

## <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo comum de menos de quadrados

Após a conclusão do treinamento:

+ Para ver os parâmetros do modelo, a saída do instrutor com o botão direito e selecione **Visualize**.

+ Para fazer previsões, ligue-se o modelo preparado para o [modelo de pontuação](./score-model.md) módulo, juntamente com um conjunto de dados de novos valores. 


## <a name="bkmk_GradientDescent"></a> Criar um modelo de regressão online descendente de gradação a utilizar

1. Adicionar a **modelo de regressão Linear** módulo à sua experimentação na interface.

    Pode encontrar este módulo na **Machine Learning** categoria. Expanda **inicializar modelo**, expanda **regressão**e arraste o **modelo de regressão Linear** módulo à sua experimentação

2. Na **propriedades** painel, na **método de solução** menu pendente lista, escolha **Online descendente de gradação** como método de cálculo utilizado para localizar a linha de regressão.

3. Para **modo de instrutor de criação**, indicar se pretende preparar o modelo com um conjunto predefinido de parâmetros ou se pretender otimizar o modelo utilizando varrimentos.

    + **Único parâmetro**: Se souber como pretende configurar a rede de regressão linear, pode fornecer um conjunto específico de valores como argumentos.

   
4. Para **taxa de aprendizagem**, especifique a velocidade de aprendizagem inicial para o otimizador de stochastic gradient descendente gradação.

5. Para **número de epochs de treinamento**, tipo de um valor que indica o número de vezes que o algoritmo deve iterar por meio de exemplos. Para conjuntos de dados com um pequeno número de exemplos, este número deve ser grande para chegar a convergência.

6. **Normalizar funcionalidades**: Se já tiver normalizados os dados numéricos usados para treinar o modelo, poderá desmarcar esta opção. Por predefinição, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Não se esqueça de aplicar o mesmo método de normalização para novos dados utilizados para a classificação.

7. Na **peso de regularização de L2**, digite o valor para utilizar como o peso de regularização de L2. Recomendamos que utilize um valor diferente de zero para evitar overfitting.

    Para saber mais sobre como regularização afeta o ajuste de modelo, consulte este artigo: [L1 e L2 regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecione a opção **taxa de aprendizagem de diminuição**, se pretender que a taxa de aprendizagem para diminuir como progresso de iterações.  

10. Para **seed número aleatório**, opcionalmente, pode escrever um valor para efetuar o seeding do gerador de números aleatórios usado pelo modelo. Usando um valor de semente é útil se pretender manter os mesmos resultados em diferentes execuções da experimentação mesmo.


12. Adicione um conjunto de dados com nome e um dos módulos de treinamento.

    Se não estiver a utilizar varrimentos, utilize o [Train Model](train-model.md) módulo.

13. Execute a experimentação.

## <a name="results-for-online-gradient-descent"></a>Resultados para online descendente de gradação

Após a conclusão do treinamento:

+ Para fazer previsões, ligue-se o modelo preparado para o [modelo de pontuação](./score-model.md) módulo, juntamente com dados de entrada novo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 