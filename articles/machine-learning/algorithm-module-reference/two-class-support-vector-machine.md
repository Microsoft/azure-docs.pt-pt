---
title: 'Máquina de Vetor com suporte de classe dois: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o **máquina de Vetor com suporte a duas classes** módulo no serviço do Azure Machine Learning para criar um modelo com base no algoritmo da máquina de vetor suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027934"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de Vetor com suporte a duas classes

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para criar um modelo com base no algoritmo da máquina de vetor suporte. 

Máquinas de vetores de suporte (SVMs) são uma classe bem pesquisada dos métodos de aprendizagem supervisionado. Essa implementação particular é adequada para a predição de dois resultados possíveis, com base nas variáveis contínuas ou categóricos.

Depois de definir os parâmetros de modelo, treiná-lo ao utilizar os módulos de treinamento e fornecendo um *etiquetados de conjunto de dados* que inclui uma coluna de etiqueta ou o resultado.

## <a name="about-support-vector-machines"></a>Sobre as máquinas de vetores de suporte

Máquinas de vetores de suporte estão entre o mais cedo possível de algoritmos de machine learning e modelos SVM tenham sido utilizados em muitas aplicações, de obtenção de informações a classificação de texto e imagem. SVMs podem ser utilizados para tarefas de classificação e regressão.

Este modelo SVM é um modelo de aprendizagem supervisionada que requer que os dados etiquetados. No processo de treinamento, o algoritmo analisa os dados de entrada e reconhece padrões num espaço de funcionalidade multidimensional chamada a *hyperplane*.  Todos os exemplos de entrada são representados como pontos neste espaço e são mapeados para categorias de tal forma que categorias são divididas pela como toda a e limpar um intervalo possível de saída.

Para predição, o algoritmo SVM atribui novos exemplos numa categoria ou outro, mapeando-os para esse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para este tipo de modelo, recomenda-se que normalizar o conjunto de dados antes de o utilizar para treinar o classificador.
  
1.  Adicionar a **máquina de Vetor com suporte a duas classes** módulo à sua experimentação.  
  
2.  Especifique como pretende que o modelo de formação, definindo a **modo de instrutor de criação** opção.  
  
    -   **Único parâmetro**: Se souber como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos.  

3.  Para **número de iterações**, escreva um número que indica o número de iterações utilizado ao criar o modelo.  
  
     Este parâmetro pode ser utilizado para controlar diferentes aspetos de velocidade de treinamento e precisão.  
  
4.  Para **Lambda**, escreva um valor a utilizar como o peso para L1 regularização.  
  
     Este coeficiente de regularização pode ser utilizado para otimizar o modelo. Valores maiores penalize modelos mais complexos.  
  
5.  Selecione a opção **normalizar funcionalidades**, se quiser normalizar funcionalidades antes de treinamento.
  
     Se aplicar a normalização, antes de treinamento, pontos de dados são centrados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção **projeto para a esfera unidade**, normalizar coeficientes.
  
     Projeção de valores para o espaço da unidade significa que antes de treinamento, pontos de dados são centrados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Na **seed número aleatório**, escreva um valor inteiro para utilizar como uma semente, se quiser garantir a capacidade de reprodução em execuções.  Caso contrário, um valor de relógio do sistema é usado como uma semente, o que pode resultar em resultados ligeiramente diferentes nas execuções.
  
9. Ligar um conjunto de dados com nome e uma da [módulos de treinamento](module-reference.md):
  
    -   Se definir **modo de instrutor de criação** para **único parâmetro**, utilize o [Train Model](train-model.md) módulo.
  

10. Execute a experimentação.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de funcionalidade aprendidos treinamento, com o botão direito a saída de [Train Model](./train-model.md)e selecione **Visualize**.

+ Para utilizar os modelos treinados para fazer previsões, ligue-se o modelo preparado para o [modelo de pontuação](score-model.md) módulo.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 