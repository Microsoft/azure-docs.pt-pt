---
title: 'Regressão linear: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo regressão linear no Azure Machine Learning para criar um modelo de regressão linear para uso em um pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f3cb583a3594b14266249ef80f8c49633c1df1de
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152198"
---
# <a name="linear-regression-module"></a>Módulo de regressão linear
Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo de regressão linear para uso em um pipeline.  A regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico, ou variável dependente. 

Você usa esse módulo para definir um método de regressão linear e, em seguida, treinar um modelo usando um DataSet rotulado. O modelo treinado pode então ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

A regressão linear é um método estatístico comum, que foi adotado no aprendizado de máquina e aprimorado com muitos métodos novos para ajustar o erro de linha e medição. No sentido mais básico, a regressão refere-se à previsão de um destino numérico. A regressão linear ainda é uma boa opção quando você deseja um modelo simples para uma tarefa de previsão básica. A regressão linear também tende a funcionar bem em conjuntos de dados esparsos e altamente dimensionais sem complexidade.

O Azure Machine Learning dá suporte a uma variedade de modelos de regressão, além da regressão linear. No entanto, o termo "regressão" pode ser interpretado livremente e alguns tipos de regressão fornecidos em outras ferramentas não têm suporte.

+ O problema de regressão clássica envolve uma única variável independente e uma variável dependente. Isto chama-se *simples regressão.*  Este módulo dá suporte à regressão simples.

+ *A regressão linear múltipla* envolve duas ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas em que várias inputs são usadas para prever um único resultado numérico também são chamados *de regressão linear multivariada.*

    O módulo **de regressão linear** pode resolver estes problemas, assim como a maioria dos outros módulos de regressão.

+ *A regressão multi-etiqueta* é a tarefa de prever múltiplas variáveis dependentes dentro de um único modelo. Por exemplo, na regressão logística de vários rótulos, um exemplo pode ser atribuído a vários rótulos diferentes. (Isso é diferente da tarefa de prever vários níveis dentro de uma única variável de classe.)

    Não há suporte para esse tipo de regressão no Azure Machine Learning. Para prever várias variáveis, crie um aprendiz separado para cada saída que você deseja prever.

Há anos, estatísticos desenvolver métodos cada vez mais avançados para regressão. Isso é verdadeiro mesmo para regressão linear. Esse módulo dá suporte a dois métodos para medir o erro e se ajustar à linha de regressão: método de quadrados mínimos comum e descendente de gradiente.

- **A descida do gradiente** é um método que minimiza a quantidade de erro em cada passo do processo de treino do modelo. Há muitas variações no descendente do gradiente e sua otimização para vários problemas de aprendizado foi amplamente estudada. Se escolher esta opção para o **método Solution,** pode definir uma variedade de parâmetros para controlar o tamanho do passo, a taxa de aprendizagem, e assim por diante. Essa opção também dá suporte ao uso de uma varredura de parâmetro integrada.

- **Quadrados mínimos comuns** é uma das técnicas mais usadas na regressão linear. Por exemplo, os quadrados mínimos são o método usado nas ferramentas de análise para o Microsoft Excel.

    Os quadrados mínimos comuns referem-se à função de perda, que computa o erro como a soma do quadrado de distância do valor real para a linha prevista e ajusta o modelo, minimizando o erro ao quadrado. Esse método assume uma relação linear forte entre as entradas e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar regressão linear

Esse módulo dá suporte a dois métodos para ajustar um modelo de regressão, com opções diferentes:

+ [Criar um modelo de regressão usando a descida do gradiente on-line](#bkmk_GradientDescent)

    O descendente de gradiente é uma função de perda melhor para modelos que são mais complexos ou que têm poucos dados de treinamento, considerando o número de variáveis.



+ [Ajuste um modelo de regressão usando quadrados mínimos comuns](#bkmk_OrdinaryLeastSquares)

    Para conjuntos de valores pequenos, é melhor selecionar quadrados mínimos comuns. Isso deve fornecer resultados semelhantes para o Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a>Criar um modelo de regressão usando quadrados comum

1. Adicione o módulo **Linear Regression Model** ao seu pipeline no designer.

    Pode encontrar este módulo na categoria **Machine Learning.** Expanda o **Modelo Inicializar,** expanda a **Regressão**e, em seguida, arraste o módulo **Linear Regression Model** para o seu pipeline.

2. No painel **Propriedades,** na lista de dropdown do **método Solução,** selecione **Quadrados Mínimos Ordinários**. Esta opção especifica o método de computação usado para localizar a linha de regressão.

3. No **peso de regularização L2,** digite o valor a utilizar como peso para regularização L2. Recomendamos que você use um valor diferente de zero para evitar o superajuste.

     Para saber mais sobre como a regularização afeta a adaptação do modelo, consulte este artigo: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecione a opção, Inclua o **termo de interceção,** se quiser ver o termo para a interceção.

    Desmarque esta opção se você não precisar revisar a fórmula de regressão.

5. Para **sementes de número aleatório,** pode escrever opcionalmente um valor para semente o gerador de números aleatório utilizado pelo modelo.

    Usar um valor de semente será útil se você quiser manter os mesmos resultados entre diferentes execuções do mesmo pipeline. Caso contrário, o padrão é usar um valor do relógio do sistema.


7. Adicione o módulo [Modelo de Comboio](./train-model.md) ao seu pipeline e ligue um conjunto de dados rotulado.

8. Executar o pipeline.

## <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo de quadrados mínimos comum

Após a conclusão do treinamento:


+ Para fazer previsões, ligue o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com um conjunto de dados de novos valores. 


## <a name="bkmk_GradientDescent"></a>Criar um modelo de regressão usando a descida do gradiente on-line

1. Adicione o módulo **Linear Regression Model** ao seu pipeline no designer.

    Pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Modelo inicializar,** expandir a **Regressão**e arraste o módulo **Linear Regression Model** para o seu pipeline

2. No painel **Properties,** na lista de abandono do **método Solution,** escolha a **Descida Gradiente Online** como método de cálculo utilizado para encontrar a linha de regressão.

3. Para criar o **modo de treinador,** indique se pretende treinar o modelo com um conjunto de parâmetros predefinidos ou se pretende otimizar o modelo utilizando uma varredura de parâmetros.

    + **Parâmetro único**: Se souber como pretende configurar a rede linear de regressão, pode fornecer um conjunto específico de valores como argumentos.

   
4. Para **a taxa de aprendizagem,** especifique a taxa de aprendizagem inicial para o optimizador de descida de gradiente estocástico.

5. Para **o número de épocas de treino,** escreva um valor que indique quantas vezes o algoritmo deve iterar através de exemplos. Para conjuntos de valores com um pequeno número de exemplos, esse número deve ser grande para alcançar a convergência.

6. **Normalizar as funcionalidades**: Se já normalizou os dados numéricos utilizados para treinar o modelo, pode desseleccionar esta opção. Por padrão, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização aos novos dados usados para pontuação.

7. No **peso de regularização L2,** digite o valor a utilizar como peso para regularização L2. Recomendamos que você use um valor diferente de zero para evitar o superajuste.

    Para saber mais sobre como a regularização afeta a adaptação do modelo, consulte este artigo: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecione a opção, Diminuir a taxa de **aprendizagem,** se quiser que a taxa de aprendizagem diminua à medida que as iterações progridem.  

10. Para **sementes de número aleatório,** pode escrever opcionalmente um valor para semente o gerador de números aleatório utilizado pelo modelo. Usar um valor de semente será útil se você quiser manter os mesmos resultados entre diferentes execuções do mesmo pipeline.


12. Adicione um DataSet rotulado e um dos módulos de treinamento.

    Se não estiver a utilizar uma varredura de parâmetros, utilize o módulo [Modelo de Comboio.](train-model.md)

13. Executar o pipeline.

## <a name="results-for-online-gradient-descent"></a>Resultados para descendente de gradiente online

Após a conclusão do treinamento:

+ Para fazer previsões, ligue o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 