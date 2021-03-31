---
title: 'Regressão florestal de decisão: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo de Regressão Florestal de Decisão no Azure Machine Learning para criar um modelo de regressão baseado num conjunto de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375368"
---
# <a name="decision-forest-regression-module"></a>Módulo de Regressão Florestal de Decisão

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão baseado num conjunto de árvores de decisão.

Depois de configurar o modelo, deve treinar o modelo utilizando um conjunto de dados rotulado e o módulo [Train Model.](./train-model.md) O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como funciona

As árvores de decisão são modelos não paramétricos que realizam uma sequência de testes simples para cada instância, atravessando uma estrutura binária de dados de árvores até que um nó folha (decisão) seja alcançado.

As árvores de decisão têm estas vantagens:

- São eficientes tanto no cálculo como no uso da memória durante o treino e a previsão.

- Podem representar limites de decisão não lineares.

- Realizam seleção e classificação integradas de recursos e são resistentes na presença de características ruidosas.

Este modelo de regressão consiste num conjunto de árvores de decisão. Cada árvore numa decisão de regressão a floresta produz uma distribuição gaussiana como uma previsão. Uma agregação é realizada sobre o conjunto de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores do modelo.

Para obter mais informações sobre o enquadramento teórico deste algoritmo e a sua implementação, consulte este artigo: [Decisão Florestas: Um Quadro Unificado para a Classificação, Regressão, Estimativa de Densidade, Aprendizagem de Variedades e Aprendizagem Semi-Supervised](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão florestal da decisão

1. Adicione o módulo **de regressão florestal** de decisão ao oleoduto. Pode encontrar o módulo no designer em **Machine Learning,** **Initialize Model** e **Regression**.

2. Abra as propriedades do módulo e, para **o método de resampling,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre **Embalar** ou **Replicar.**

    - **Embalamento**: O embalamento também é chamado de *agregador de botas.* Cada árvore numa decisão de regressão a floresta produz uma distribuição gaussiana por previsão. A agregação é encontrar um gaussiano cujos dois primeiros momentos coincidem com os momentos da mistura de distribuições gaussianas dadas pela combinação de todas as distribuições devolvidas por árvores individuais.

         Para mais informações, consulte a entrada na Wikipédia para [a agregação de Bootstrap.](https://wikipedia.org/wiki/Bootstrap_aggregating)

    - **Replicação:** Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatório e as árvores serão diversas.

         Para obter mais informações sobre o processo de formação com a opção **Replicate,** consulte [As Florestas de Decisão para Visão Computacional e Análise de Imagem Médica. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/). .

3. Especifique como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**

    - **Parâmetro único**

      Se sabe como pretende configurar o modelo, pode fornecer um conjunto específico de valores como argumentos. Pode ter aprendido estes valores experimentando ou recebido como orientação.

    - **Intervalo de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretender fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) itera sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparmetros que produzem os melhores resultados. 



4. Para **o número de árvores de decisão,** indicar o número total de árvores de decisão a criar no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treino vai aumentar.

    > [!TIP]
    > Se definir o valor para 1; no entanto, isto significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e não serão realizadas mais iterações.

5. Para **a máxima profundidade das árvores de decisão,** escreva um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, correndo o risco de alguma adaptação excessiva e maior tempo de treino.

6. Para **o número de divisões aleatórias por nó,** digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

7. Para **o número mínimo de amostras por nó folha,** indique o número mínimo de caixas necessárias para criar qualquer nó terminal (folha) numa árvore.

     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos cinco casos que satisfaçam as mesmas condições.


9. Treine o modelo:

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

### <a name="results"></a>Resultados

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o módulo de treino e, em seguida, mude para o **separador Saídas** no painel direito. Clique no modelo de registo de **ícones.**  Pode encontrar o modelo guardado como um módulo na árvore do módulo. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 