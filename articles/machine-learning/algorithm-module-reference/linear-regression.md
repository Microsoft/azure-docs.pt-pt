---
title: 'Regressão Linear: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão linear em Azure Machine Learning para criar um modelo linear de regressão para uso num oleoduto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd634c41a1d6e3d9846e8299dd281b52beb77130
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912794"
---
# <a name="linear-regression-module"></a>Módulo de regressão linear
Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo linear de regressão para utilização num oleoduto.  A regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico, ou variável dependente. 

Usa este módulo para definir um método linear de regressão e, em seguida, treina um modelo usando um conjunto de dados rotulado. O modelo treinado pode então ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

A regressão linear é um método estatístico comum, que foi adotado na aprendizagem automática e melhorado com muitos novos métodos para encaixar a linha e medir o erro. No sentido mais básico, a regressão refere-se à previsão de um alvo numérico. A regressão linear ainda é uma boa escolha quando se quer um modelo simples para uma tarefa preditiva básica. A regressão linear também tende a funcionar bem em conjuntos de dados de alta dimensão e escassos, sem complexidade.

O Azure Machine Learning suporta uma variedade de modelos de regressão, além da regressão linear. No entanto, o termo "regressão" pode ser interpretado vagamente, e alguns tipos de regressão fornecidos noutras ferramentas não são suportados.

+ O problema clássico de regressão envolve uma única variável independente e uma variável dependente. Isto chama-se *simples regressão.*  Este módulo suporta uma simples regressão.

+ *A regressão linear múltipla* envolve duas ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas em que várias inputs são usadas para prever um único resultado numérico também são chamados *de regressão linear multivariada.*

    O módulo **de regressão linear** pode resolver estes problemas, assim como a maioria dos outros módulos de regressão.

+ *A regressão multi-etiqueta* é a tarefa de prever múltiplas variáveis dependentes dentro de um único modelo. Por exemplo, em regressão logística multi-etiqueta, uma amostra pode ser atribuída a vários rótulos diferentes. (Isto é diferente da tarefa de prever múltiplos níveis dentro de uma única variável de classe.)

    Este tipo de regressão não é suportado na Aprendizagem automática azure. Para prever várias variáveis, crie um aprendiz separado para cada saída que deseje prever.

Há anos que os estatísticos têm vindo a desenvolver métodos cada vez mais avançados de regressão. Isto é verdade mesmo para regressão linear. Este módulo suporta dois métodos para medir o erro e encaixar na linha de regressão: método comum menos quadrados e descida de gradiente.

- **A descida do gradiente** é um método que minimiza a quantidade de erro em cada passo do processo de treino do modelo. Existem muitas variações na descida do gradiente e a sua otimização para vários problemas de aprendizagem tem sido extensivamente estudada. Se escolher esta opção para o **método Solution,** pode definir uma variedade de parâmetros para controlar o tamanho do passo, a taxa de aprendizagem, e assim por diante. Esta opção também suporta a utilização de uma varredura de parâmetros integrado.

- **Quadrados mínimos comuns** é uma das técnicas mais usadas na regressão linear. Por exemplo, o menor conjunto é o método que é usado no Analysis Toolpak para o Microsoft Excel.

    Os quadrados mínimos comuns referem-se à função de perda, que calcula o erro como a soma do quadrado de distância do valor real para a linha prevista, e se encaixa no modelo minimizando o erro quadrado. Este método assume uma forte relação linear entre as inputs e a variável dependente.

## <a name="configure-linear-regression"></a>Reconfigurar a regressão linear

Este módulo suporta dois métodos para a montagem de um modelo de regressão, com diferentes opções:

+ [Ajuste um modelo de regressão usando quadrados mínimos comuns](#create-a-regression-model-using-ordinary-least-squares)

    Para pequenos conjuntos de dados, o melhor é selecionar os quadrados comum menos quadrados. Isto deve dar resultados semelhantes ao Excel.
    
+ [Criar um modelo de regressão usando a descida do gradiente on-line](#create-a-regression-model-using-online-gradient-descent)

    A descida do gradiente é uma melhor função de perda para modelos mais complexos, ou que têm poucos dados de formação dado o número de variáveis.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Criar um modelo de regressão usando quadrados comum

1. Adicione o módulo **Linear Regression Model** ao seu pipeline no designer.

    Pode encontrar este módulo na categoria **Machine Learning.** Expanda o **Modelo Inicializar,** expanda a **Regressão**e, em seguida, arraste o módulo **Linear Regression Model** para o seu pipeline.

2. No painel **Propriedades,** na lista de dropdown do **método Solução,** selecione **Quadrados Mínimos Ordinários**. Esta opção especifica o método de cálculo utilizado para encontrar a linha de regressão.

3. No **peso de regularização L2,** digite o valor a utilizar como peso para regularização L2. Recomendamos que utilize um valor não nulo para evitar o excesso de encaixe.

     Para saber mais sobre como a regularização afeta a adaptação do modelo, consulte este artigo: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecione a opção, Inclua o **termo de interceção,** se quiser ver o termo para a interceção.

    Desmarque esta opção se não precisar de rever a fórmula de regressão.

5. Para **sementes de número aleatório,** pode escrever opcionalmente um valor para semente o gerador de números aleatório utilizado pelo modelo.

    A utilização de um valor de semente é útil se pretender manter os mesmos resultados em diferentes percursos do mesmo oleoduto. Caso contrário, a predefinição é utilizar um valor a partir do relógio do sistema.


7. Adicione o módulo [Modelo de Comboio](./train-model.md) ao seu pipeline e ligue um conjunto de dados rotulado.

8. Executar o pipeline.

### <a name="results-for-ordinary-least-squares-model"></a>Resultados do modelo comum menos quadrados

Após o treino estar completo:


+ Para fazer previsões, ligue o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com um conjunto de dados de novos valores. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Criar um modelo de regressão usando a descida do gradiente on-line

1. Adicione o módulo **Linear Regression Model** ao seu pipeline no designer.

    Pode encontrar este módulo na categoria **Machine Learning.** Expanda **o Modelo inicializar,** expandir a **Regressão**e arraste o módulo **Linear Regression Model** para o seu pipeline

2. No painel **Properties,** na lista de abandono do **método Solution,** escolha a **Descida Gradiente Online** como método de cálculo utilizado para encontrar a linha de regressão.

3. Para criar o **modo de treinador,** indique se pretende treinar o modelo com um conjunto de parâmetros predefinidos ou se pretende otimizar o modelo utilizando uma varredura de parâmetros.

    + **Parâmetro único**: Se souber como pretende configurar a rede linear de regressão, pode fornecer um conjunto específico de valores como argumentos.
    
    + **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.  

   
4. Para **a taxa de aprendizagem,** especifique a taxa de aprendizagem inicial para o optimizador de descida de gradiente estocástico.

5. Para **o número de épocas de treino,** escreva um valor que indique quantas vezes o algoritmo deve iterar através de exemplos. Para conjuntos de dados com um pequeno número de exemplos, este número deve ser grande para alcançar a convergência.

6. **Normalizar as funcionalidades**: Se já normalizou os dados numéricos utilizados para treinar o modelo, pode desseleccionar esta opção. Por padrão, o módulo normaliza todas as inputs numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização a novos dados utilizados para a pontuação.

7. No **peso de regularização L2,** digite o valor a utilizar como peso para regularização L2. Recomendamos que utilize um valor não nulo para evitar o excesso de encaixe.

    Para saber mais sobre como a regularização afeta a adaptação do modelo, consulte este artigo: [L1 e L2 Regularização para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecione a opção, Diminuir a taxa de **aprendizagem,** se quiser que a taxa de aprendizagem diminua à medida que as iterações progridem.  

10. Para **sementes de número aleatório,** pode escrever opcionalmente um valor para semente o gerador de números aleatório utilizado pelo modelo. A utilização de um valor de semente é útil se pretender manter os mesmos resultados em diferentes percursos do mesmo oleoduto.


12. Adicione um conjunto de dados rotulado e um dos módulos de treino.

    Se não estiver a utilizar uma varredura de parâmetros, utilize o módulo [Modelo de Comboio.](train-model.md)

13. Executar o pipeline.

### <a name="results-for-online-gradient-descent"></a>Resultados da descida do gradiente on-line

Após o treino estar completo:

+ Para fazer previsões, ligue o modelo treinado ao módulo [Score Model,](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 