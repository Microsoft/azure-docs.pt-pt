---
title: 'Regressão de Poisson: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão poisson no designer de Aprendizagem automática Azure para criar um modelo de regressão poisson.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420704"
---
# <a name="poisson-regression"></a>Regressão de Poisson

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão poisson num oleoduto. A regressão de Poisson destina-se à previsão de valores numéricos, tipicamente conta. Portanto, deve utilizar este módulo para criar o seu modelo de regressão apenas se os valores que está a tentar prever se encaixarem nas seguintes condições:

- A variável de resposta tem uma [distribuição Poisson.](https://en.wikipedia.org/wiki/Poisson_distribution)  

- Os condes não podem ser negativos. O método falhará completamente se tentar usá-lo com etiquetas negativas.

- Uma distribuição Poisson é uma distribuição discreta; portanto, não faz sentido utilizar este método com números não inteiros.

> [!TIP]
> Se o seu alvo não é uma contagem, a regressão de Poisson provavelmente não é um método apropriado. Experimente [outros módulos de regressão no designer.](./module-reference.md#machine-learning-algorithms) 

Depois de configurar o método de regressão, deve treinar o modelo usando um conjunto de dados que contenha exemplos do valor que pretende prever. O modelo treinado pode então ser usado para fazer previsões.

## <a name="more-about-poisson-regression"></a>Mais sobre a regressão de Poisson

A regressão de Poisson é um tipo especial de análise de regressão que é normalmente usada para a contagem de modelos. Por exemplo, a regressão de Poisson seria útil nestes cenários:

- Modelar o número de constipações associadas a voos de avião

- Estimando o número de chamadas de serviço de emergência durante um evento

- Projetar o número de inquéritos aos clientes após uma promoção

- Criação de mesas de contingência

Como a variável de resposta tem uma distribuição poisson, o modelo faz pressupostos diferentes sobre os dados e a sua distribuição de probabilidade do que, digamos, a regressão de quadrados mínimos. Por isso, os modelos Poisson devem ser interpretados de forma diferente de outros modelos de regressão.

## <a name="how-to-configure-poisson-regression"></a>Como configurar a Regressão de Poisson

1. Adicione o módulo **de Regressão Poisson** ao seu pipeline no designer. Pode encontrar este módulo em **algoritmos de aprendizagem automática,** na categoria **Regressão.**

2. Adicione um conjunto de dados que contenha dados de treino do tipo correto. 

    Recomendamos que utilize [dados de normalização](normalize-data.md) para normalizar o conjunto de dados de entrada antes de o utilizar para treinar o regressor.

3. No painel direito do módulo **de regressão poisson, especifique** como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    - **Parâmetro único**: Se souber como pretende configurar o modelo, forneça um conjunto específico de valores como argumentos.
  
    - **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, faça uma varredura de parâmetros utilizando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) O treinador itera sobre vários valores que especifica para encontrar a configuração ideal.
  
4. **Tolerância à otimização**: Digite um valor que defina o intervalo de tolerância durante a otimização. Quanto mais baixo o valor, mais lento e preciso o encaixe.

5. **Peso de regularização L1** e **peso de regularização L2**: Valores de tipo a utilizar para regularização L1 e L2. *A regularização* adiciona constrangimentos ao algoritmo em relação a aspetos do modelo que são independentes dos dados de formação. A regularização é comumente usada para evitar a sobremontagem. 

    - A regularização L1 é útil se o objetivo é ter um modelo o mais escasso possível.

        A regularização L1 é feita subtraindo o peso L1 do vetor de peso da expressão de perda que o aluno está tentando minimizar. A norma L1 é uma boa aproximação à norma L0, que é o número de coordenadas não-zero.

    - A regularização L2 impede que qualquer coordenada única no vetor de peso cresça demasiado em magnitude. A regularização L2 é útil se o objetivo é ter um modelo com pequenos pesos globais.

    Neste módulo, pode aplicar uma combinação de regularizações L1 e L2. Ao combinar a regularização L1 e L2, pode impor uma penalização à magnitude dos valores dos parâmetros. O aluno tenta minimizar o penálti, numa troca com a minimização da perda.

    Para uma boa discussão sobre a regularização L1 e L2, consulte [a Regularização L1 e L2 para machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Tamanho da memória para L-BFGS**: Especifique a quantidade de memória para reservar para a montagem e otimização do modelo.

     L-BFGS é um método específico de otimização, baseado no algoritmo Broyden-Fletcher-Goldfarb-Shanno (BFGS). O método utiliza uma quantidade limitada de memória (L) para calcular a direção do passo seguinte.

     Ao alterar este parâmetro, pode afetar o número de posições e gradientes passados que são armazenados para calcular o passo seguinte.

7. Ligue o conjunto de dados de treino e o modelo não treinado a um dos módulos de treino: 

    - Se definir **Criar modo de treinador** para single **parâmetro,** utilize o módulo Modelo de [Comboio.](train-model.md)

    - Se definir **Criar o modo de treinador** para a gama de **parâmetros,** utilize o módulo [de hiperparametros do modelo de melodia.](tune-model-hyperparameters.md)

    > [!WARNING]
    > 
    > - Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o primeiro valor na lista de parâmetros.
    > 
    > - Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.
    > 
    > - Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

8.  Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o módulo de treino e, em seguida, mude para o **separador Outputs+logs** no painel direito. Clique no **conjunto de dados** do registo do ícone .  Pode encontrar o modelo guardado como um módulo na árvore do módulo. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.