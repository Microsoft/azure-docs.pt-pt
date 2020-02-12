---
title: 'Computador de vetor de suporte de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de **máquina vetorial de suporte de duas classes** em Azure Machine Learning para criar um modelo baseado no algoritmo da máquina de vetor de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 072809949badb08e5b1e7078566e289c5a5fecd9
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153677"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de vetor de suporte de duas classes

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para criar um modelo baseado no algoritmo máquina de vetor de suporte. 

As máquinas de vetor de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizado supervisionados. Essa implementação específica é adequada à previsão de dois resultados possíveis, com base em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo utilizando os módulos de treino e fornecendo um conjunto de *dados marcado* que inclua uma etiqueta ou coluna de resultados.

## <a name="about-support-vector-machines"></a>Sobre as máquinas de vetor de suporte

Os computadores de vetor de suporte estão entre os mais antigos dos algoritmos de aprendizado de máquina, e os modelos SVM foram usados em muitos aplicativos, da recuperação de informações à classificação de texto e imagem. SVMs pode ser usado para tarefas de classificação e regressão.

Esse modelo de SVM é um modelo de aprendizado supervisionado que requer dados rotulados. No processo de treino, o algoritmo analisa os dados de entrada e reconhece padrões num espaço multidimensional chamado *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço e são mapeados para categorias de saída de tal forma que as categorias são divididas por uma grande e limpam uma lacuna possível.

Para previsão, o algoritmo SVM atribui novos exemplos em uma categoria ou na outra, mapeando-os para esse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para esse tipo de modelo, é recomendável que você Normalize o conjunto de os antes de usá-lo para treinar o classificador.
  
1.  Adicione o módulo de **máquina de vetores de suporte de duas classes** ao seu oleoduto.  
  
2.  Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**  
  
    -   **Parâmetro único**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

3.  Para **o número de iterações,** escreva um número que denote o número de iterações usadas na construção do modelo.  
  
     Esse parâmetro pode ser usado para controlar a compensação entre a precisão e a velocidade de treinamento.  
  
4.  Para **a Lambda,** digite um valor a utilizar como peso para regularização L1.  
  
     Esse coeficiente de regularização pode ser usado para ajustar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção, **normalize as funcionalidades,** se quiser normalizar as funcionalidades antes do treino.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados serão centralizados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção, **Project para a esfera unitária,** para normalizar os coeficientes.
  
     A projeção de valores para o espaço de unidade significa que, antes do treinamento, os pontos de dados são centralizados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Em **sementes de número aleatórios,** digite um valor inteiro para usar como semente se quiser garantir a reprodutibilidade em corridas.  Caso contrário, um valor de relógio do sistema será usado como uma semente, o que pode resultar em resultados ligeiramente diferentes entre as execuções.
  
9. Ligue um conjunto de dados rotulado e um dos módulos de [treino:](module-reference.md)
  
    -   Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](train-model.md)
  
10. Executar o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.

+ Para utilizar o modelo para a pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 