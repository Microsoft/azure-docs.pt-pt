---
title: 'Regressão Linear: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo linear de regressão em Azure Machine Learning para criar um modelo linear de regressão para utilização num oleoduto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323653"
---
# <a name="linear-regression-module"></a>Módulo linear de regressão
Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo linear de regressão para utilização num oleoduto.  A regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico, ou variável dependente. 

Utiliza este módulo para definir um método linear de regressão e, em seguida, treina um modelo utilizando um conjunto de dados rotulado. O modelo treinado pode então ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

A regressão linear é um método estatístico comum, que foi adotado na aprendizagem automática e melhorado com muitos novos métodos para encaixar a linha e medir o erro. Simplificando, a regressão refere-se à previsão de um alvo numérico. A regressão linear ainda é uma boa escolha quando se quer um modelo simples para uma tarefa preditiva básica. A regressão linear também tende a funcionar bem em conjuntos de dados de alta dimensão e escassos que carecem de complexidade.

A Azure Machine Learning suporta uma variedade de modelos de regressão, além da regressão linear. No entanto, o termo "regressão" pode ser interpretado vagamente, e alguns tipos de regressão fornecida noutras ferramentas não são suportados.

+ O problema clássico da regressão envolve uma única variável independente e uma variável dependente. Isto *chama-se regressão simples.*  Este módulo suporta uma regressão simples.

+ *A regressão linear múltipla* envolve duas ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas em que várias entradas são usadas para prever um único resultado numérico são também *chamados regressão linear multivariada*.

    O módulo **linear de regressão** pode resolver estes problemas, assim como a maioria dos outros módulos de regressão.

+ *A regressão multi-rótulo* é a tarefa de prever múltiplas variáveis dependentes dentro de um único modelo. Por exemplo, na regressão logística multi-rótulo, uma amostra pode ser atribuída a vários rótulos diferentes. (Isto é diferente da tarefa de prever vários níveis dentro de uma única classe variável.)

    Este tipo de regressão não é suportado no Azure Machine Learning. Para prever várias variáveis, crie um aprendiz separado para cada saída que deseja prever.

Há anos que os estatísticos desenvolvem métodos cada vez mais avançados para a regressão. Isto é verdade mesmo para a regressão linear. Este módulo suporta dois métodos para medir o erro e encaixar na linha de regressão: método comum mínimo quadrados e descida de gradiente.

- **A descida de gradiente** é um método que minimiza a quantidade de erro em cada passo do processo de treino do modelo. Existem muitas variações na descida do gradiente e a sua otimização para vários problemas de aprendizagem tem sido amplamente estudada. Se escolher esta opção para **o método Solution,** pode definir uma variedade de parâmetros para controlar o tamanho do passo, a taxa de aprendizagem, etc. Esta opção também suporta a utilização de uma varredura integrada de parâmetros.

- **Os quadrados mínimos comuns** são uma das técnicas mais usadas na regressão linear. Por exemplo, o mínimo quadrados é o método que é usado no Toolpak de análise para o Microsoft Excel.

    Os quadrados mínimos comuns referem-se à função de perda, que calcula o erro como a soma do quadrado de distância do valor real para a linha prevista, e se encaixa no modelo minimizando o erro quadrado. Este método pressupõe uma forte relação linear entre as entradas e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar a Regressão Linear

Este módulo suporta dois métodos para a montagem de um modelo de regressão, com diferentes opções:

+ [Encaixe um modelo de regressão usando quadrados mínimos comuns](#create-a-regression-model-using-ordinary-least-squares)

    Para pequenos conjuntos de dados, é melhor selecionar quadrados comuns. Isto deve dar resultados semelhantes ao Excel.
    
+ [Criar um modelo de regressão utilizando a descida do gradiente on-line](#create-a-regression-model-using-online-gradient-descent)

    A descida de gradiente é uma melhor função de perda para modelos que são mais complexos, ou que têm poucos dados de treino dado o número de variáveis.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Criar um modelo de regressão usando quadrados mínimos comuns

1. Adicione o módulo **Modelo linear de regressão** ao seu oleoduto no designer.

    Pode encontrar este módulo na categoria **machine learning.** Expandir **o Modelo Inicialize,** expandir **a Regressão** e, em seguida, arrastar o módulo **Modelo linear de Regressão** para o seu oleoduto.

2. No painel **propriedades,** na lista de dropdown do **método Solution,** selecione **Os Quadrados Mínimos Comuns**. Esta opção especifica o método de cálculo utilizado para encontrar a linha de regressão.

3. No **peso de regularização L2,** digite o valor a utilizar como peso para a regularização L2. Recomendamos que utilize um valor não zero para evitar a sobremontagem.

     Para saber mais sobre como a regularização afeta a montagem do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Selecione a opção, Inclua o **termo de interceção,** se quiser visualizar o termo para a interceção.

    Desescole esta opção se não precisar de rever a fórmula de regressão.

5. Para **sementes de número aleatório,** pode, opcionalmente, escrever um valor para semear o gerador de números aleatórios utilizado pelo modelo.

    A utilização de um valor de sementes é útil se pretender manter os mesmos resultados em diferentes percursos do mesmo oleoduto. Caso contrário, o padrão é utilizar um valor do relógio do sistema.


7. Adicione o módulo [Modelo de Comboio](./train-model.md) ao seu oleoduto e conecte um conjunto de dados rotulado.

8. Envie o oleoduto.

### <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo comum de menos quadrados

Após o treino estar completo:


+ Para fazer previsões, ligue o modelo treinado ao módulo ['Modelo de Pontuação',](./score-model.md) juntamente com um conjunto de dados de novos valores. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Criar um modelo de regressão utilizando a descida do gradiente on-line

1. Adicione o módulo **Modelo linear de regressão** ao seu oleoduto no designer.

    Pode encontrar este módulo na categoria **machine learning.** Expandir **o Modelo Inicialize,** expandir **a Regressão,** e arrastar o módulo **Modelo linear de Regressão** para o seu pipeline

2. No painel **propriedades,** na lista de dropdown do **método Solution,** escolha **a Descida De Gradiente Online** como o método de cálculo utilizado para encontrar a linha de regressão.

3. Para **criar o modo de treinador,** indique se pretende treinar o modelo com um conjunto de parâmetros predefinidos ou se pretende otimizar o modelo utilizando uma varredura de parâmetros.

    + **Parâmetro único** : Se souber como pretende configurar a rede linear de regressão, pode fornecer um conjunto específico de valores como argumentos.
    
    + **Intervalo de parâmetros** : Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados.  

   
4. Para **a taxa de aprendizagem** , especifique a taxa inicial de aprendizagem para o otimizador de descida de gradiente estocástico.

5. Para **o Número de épocas de treino,** escreva um valor que indique quantas vezes o algoritmo deve iterar através de exemplos. Para conjuntos de dados com um pequeno número de exemplos, este número deve ser grande para alcançar a convergência.

6. **Normalizar as características** : Se já normalizou os dados numéricos utilizados para treinar o modelo, pode desmarcar esta opção. Por predefinição, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização aos novos dados utilizados para a pontuação.

7. No **peso de regularização L2,** digite o valor a utilizar como peso para a regularização L2. Recomendamos que utilize um valor não zero para evitar a sobremontagem.

    Para saber mais sobre como a regularização afeta a montagem do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Selecione a opção, **Diminua a taxa de aprendizagem,** se quiser que a taxa de aprendizagem diminua à medida que as iterações progridem.  

10. Para **sementes de número aleatório,** pode, opcionalmente, escrever um valor para semear o gerador de números aleatórios utilizado pelo modelo. A utilização de um valor de sementes é útil se pretender manter os mesmos resultados em diferentes percursos do mesmo oleoduto.


12. Treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

13. Envie o oleoduto.

### <a name="results-for-online-gradient-descent"></a>Resultados para descida de gradiente on-line

Após o treino estar completo:

+ Para fazer previsões, ligue o modelo treinado ao módulo ['Modelo de Pontuação',](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.