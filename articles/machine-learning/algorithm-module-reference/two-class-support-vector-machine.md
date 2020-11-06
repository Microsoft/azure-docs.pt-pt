---
title: 'Two-Class máquina de vetor de suporte: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Two-Class de suporte da máquina de vetor em Azure Machine Learning para criar um classificador binário.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 46cfdd319fc89e569d165dc2e11303e67c6dd54e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420568"
---
# <a name="two-class-support-vector-machine-module"></a>módulo de máquina de vetor de suporte Two-Class

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Use este módulo para criar um modelo que se baseie no algoritmo da máquina vetorial de suporte. 

As máquinas vetoriais de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizagem supervisionados. Esta implementação específica é adequada à previsão de dois resultados possíveis, baseados em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo utilizando os módulos de treino e forneça um *conjunto de dados marcado* que inclua uma etiqueta ou coluna de resultados.

## <a name="about-support-vector-machines"></a>Sobre máquinas vetores de apoio

As máquinas de vetor de suporte estão entre os primeiros algoritmos de aprendizagem automática, e os modelos SVM têm sido usados em muitas aplicações, desde a recuperação de informações até à classificação de texto e imagem. Os SVMs podem ser utilizados tanto para tarefas de classificação como para regressão.

Este modelo SVM é um modelo de aprendizagem supervisionado que requer dados rotulados. No processo de treino, o algoritmo analisa dados de entrada e reconhece padrões num espaço multidimensional chamado *hiperplano.*  Todos os exemplos de entrada são representados como pontos neste espaço, e são mapeados para categorias de saída de modo a que as categorias sejam divididas por uma lacuna o mais ampla e clara possível.

Para previsão, o algoritmo SVM atribui novos exemplos numa categoria ou noutra, mapeando-os nesse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para este tipo de modelo, recomenda-se que normalize o conjunto de dados antes de o utilizar para treinar o classificador.
  
1.  Adicione o módulo **de máquina de vetor de suporte de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    -   **Parâmetro único** : Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros** : Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo [De Hiperparametros Tune Model.](tune-model-hyperparameters.md) Fornece alguma gama de valores, e o treinador itera sobre várias combinações das definições para determinar a combinação de valores que produz o melhor resultado.

3.  Para **o número de iterações,** escreva um número que denota o número de iterações utilizadas na construção do modelo.  
  
     Este parâmetro pode ser utilizado para controlar o trade-off entre a velocidade de treino e a precisão.  
  
4.  Para **lambda,** escreva um valor a utilizar como peso para a regularização L1.  
  
     Este coeficiente de regularização pode ser usado para afinar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção, **normalize as funcionalidades,** se quiser normalizar as funcionalidades antes do treino.
  
     Se aplicar a normalização, antes do treino, os pontos de dados são centrados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção, **Projetar para a esfera da unidade,** para normalizar os coeficientes.
  
     Projetar valores para o espaço unitário significa que antes do treino, os pontos de dados são centrados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Em **sementes de número aleatório,** escreva um valor inteiro para usar como semente se quiser garantir a reprodutibilidade através de corridas.  Caso contrário, um valor do relógio do sistema é usado como uma semente, o que pode resultar em resultados ligeiramente diferentes em todas as corridas.
  
9. Ligue um conjunto de dados rotulado e treine o modelo:

    + Se definir **Criar o modo de treinador** para single **parâmetro,** conecte um conjunto de dados marcado e o módulo Modelo de [Comboio.](train-model.md)  
  
    + Se definir **Criar modo de treinador** para intervalo de **parâmetros,** ligue um conjunto de dados marcado e treine o modelo utilizando [hiperparmetros do modelo de sintonização](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o valor predefinido na lista de parâmetros únicos.  
    > 
    > Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.  
    > 
    > Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.
  
10. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para pontuar, adicione o módulo **'Marcar',** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 