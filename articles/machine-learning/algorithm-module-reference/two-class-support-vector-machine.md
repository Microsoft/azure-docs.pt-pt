---
title: 'Máquina vetorial de suporte de duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de **máquina vetorial de suporte de duas classes** em Azure Machine Learning para criar um modelo baseado no algoritmo da máquina de vetor de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3b2f6b2bb1dc5f9e16c537f78b5a456ee4984e80
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916732"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de vetor de suporte de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo baseado no algoritmo da máquina de vetorial de suporte. 

As máquinas vetoriais de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizagem supervisionados. Esta implementação específica é adequada à previsão de dois resultados possíveis, baseados em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo utilizando os módulos de treino e fornecendo um conjunto de *dados marcado* que inclua uma etiqueta ou coluna de resultados.

## <a name="about-support-vector-machines"></a>Sobre máquinas vetoriais de suporte

As máquinas vetoriais de suporte estão entre as primeiras algoritmos de aprendizagem automática, e os modelos SVM têm sido usados em muitas aplicações, desde a recuperação de informação até à classificação de texto e imagem. Os SVMs podem ser usados tanto para tarefas de classificação como para a regressão.

Este modelo SVM é um modelo de aprendizagem supervisionado que requer dados rotulados. No processo de treino, o algoritmo analisa os dados de entrada e reconhece padrões num espaço multidimensional chamado *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço, e são mapeados para categorias de produção de modo a que as categorias sejam divididas pelo mais amplo e claro intervalo possível.

Para previsão, o algoritmo SVM atribui novos exemplos numa categoria ou noutra, mapeando-os para esse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para este tipo de modelo, recomenda-se que normalize o conjunto de dados antes de o utilizar para treinar o classificador.
  
1.  Adicione o módulo de **máquina de vetores de suporte de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

    -   **Gama de parâmetros**: Se não tiver a certeza dos melhores parâmetros, pode encontrar os parâmetros ideais utilizando o módulo de [hiperparâmetros](tune-model-hyperparameters.md) do Modelo tune. Você fornece alguma gama de valores, e o treinador iterates sobre múltiplas combinações das configurações para determinar a combinação de valores que produz o melhor resultado.

3.  Para **o número de iterações,** escreva um número que denote o número de iterações usadas na construção do modelo.  
  
     Este parâmetro pode ser utilizado para controlar a compensação entre a velocidade de treino e a precisão.  
  
4.  Para **a Lambda,** digite um valor a utilizar como peso para regularização L1.  
  
     Este coeficiente de regularização pode ser usado para afinar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção, **normalize as funcionalidades,** se quiser normalizar as funcionalidades antes do treino.
  
     Se aplicar a normalização, antes do treino, os pontos de dados são centrados na média e escalados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção, **Project para a esfera unitária,** para normalizar os coeficientes.
  
     Projetar valores para o espaço unitário significa que antes do treino, os pontos de dados são centrados em 0 e escalados para ter uma unidade de desvio padrão.
  
7.  Em **sementes de número aleatórios,** digite um valor inteiro para usar como semente se quiser garantir a reprodutibilidade em corridas.  Caso contrário, o valor do relógio do sistema é usado como uma semente, o que pode resultar em resultados ligeiramente diferentes em corridas.
  
9. Ligue um conjunto de dados rotulado e um dos módulos de [treino:](module-reference.md)
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](train-model.md)
  
10. Executar o pipeline.

## <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 