---
title: 'Fast Forest Quantile Regression: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão quântil da floresta rápida para criar um modelo de regressão que possa prever valores para um número especificado de quantiles.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907956"
---
# <a name="fast-forest-quantile-regression"></a>Regressão de Quantil da Floresta Rápida

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para criar um modelo de regressão quântil de floresta rápida num oleoduto. A regressão quântil da floresta rápida é útil se quiser entender mais sobre a distribuição do valor previsto, em vez de obter um único valor médio de previsão. Este método tem muitas aplicações, incluindo:  
  
- Previsão de preços  
  
- Estimar o desempenho dos alunos ou aplicar gráficos de crescimento para avaliar o desenvolvimento infantil  
  
- Descobrir relações preditivas em casos em que há apenas uma relação fraca entre variáveis  
  
Este algoritmo de regressão é um método de aprendizagem **supervisionado,** o que significa que requer um conjunto de dados marcado que inclui uma coluna de etiqueta. Por ser um algoritmo de regressão, a coluna do rótulo deve conter apenas valores numéricos.

## <a name="more-about-quantile-regression"></a>Mais sobre regressão quântica

Há muitos tipos diferentes de regressão. Simplificando, regressão significa encaixar um modelo num alvo expresso como vetor numérico. No entanto, os estatísticos têm vindo a desenvolver métodos cada vez mais avançados para a regressão.

A definição mais simples de *quântil* é um valor que divide um conjunto de dados em grupos de tamanho igual; assim, os valores quânticos marcam as fronteiras entre grupos. Estatisticamente falando, os quânticos são valores tomados a intervalos regulares a partir do inverso da função de distribuição cumulativa (CDF) de uma variável aleatória.

Enquanto os modelos lineares de regressão tentam prever o valor de uma variável numérica usando uma única estimativa, a *média*, às vezes é necessário prever a gama ou distribuição total da variável-alvo. Para este fim, foram desenvolvidas técnicas como a regressão bayesiana e a regressão quântica.

A regressão quântica ajuda-o a compreender a distribuição do valor previsto. Os modelos de regressão quântico à base de árvores, como o utilizado neste módulo, têm a vantagem adicional de poderem ser utilizados para prever distribuições não paramétricas.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Como configurar a regressão quântil da floresta rápida

1. Adicione o módulo **de regressão Quantile De Floresta Rápida** ao seu oleoduto no designer. Pode encontrar este módulo em **algoritmos de aprendizagem automática,** na categoria **Regressão.**

2. No painel direito do módulo **de Regressão Quantile De Floresta Rápida, especifique** como pretende que o modelo seja treinado, definindo a opção **modo de formação Create.**  
  
    - **Parâmetro único**: Se souber como pretende configurar o modelo, forneça um conjunto específico de valores como argumentos. Quando treinar o modelo, use [o Modelo de Comboio.](train-model.md)
  
    - **Intervalo de parâmetros**: Se não tiver a certeza dos melhores parâmetros, faça uma varredura de parâmetros utilizando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) O treinador itera sobre vários valores que especifica para encontrar a configuração ideal.

3. **Número de Árvores,** escreva o número máximo de árvores que podem ser criadas no conjunto. Se criar mais árvores, geralmente leva a uma maior precisão, mas à custa de um tempo de treino mais longo.  

4. **Número de Folhas**, digite o número máximo de folhas, ou nóns terminais, que podem ser criados em qualquer árvore.  

5. **Número mínimo de casos de formação necessários para formar uma folha,** especifique o número mínimo de exemplos necessários para criar qualquer nó terminal (folha) numa árvore.  
  
     Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados da formação terão de conter pelo menos 5 casos que satisfaçam as mesmas condições.

6. **Fração de ensaqueimento**, especifique um número entre 0 e 1 que represente a fração de amostras a utilizar na construção de cada grupo de quânticos. As amostras são escolhidas aleatoriamente, com substituição.

7. **Fração dividida,** digite um número entre 0 e 1 que representa a fração de características a utilizar em cada divisão da árvore. As características utilizadas são sempre escolhidas aleatoriamente.

8. **Quantiles a estimar**, escreva uma lista separada de pontos de semente dos quâneos para os quais pretende que o modelo treine e crie previsões.
  
     Por exemplo, se quiser construir um modelo que se calcule para os quarteis, escreveria `0.25; 0.5; 0.75` .  

9. Opcionalmente, digite um valor para **sementes de número aleatório** para semear o gerador de número aleatório utilizado pelo modelo.  O padrão é 0, o que significa que uma semente aleatória é escolhida.
  
     Deve fornecer um valor se precisar de reproduzir resultados em sucessivas execuções nos mesmos dados.  

10. Ligue o conjunto de dados de treino e o modelo não treinado a um dos módulos de treino: 

    - Se definir **Criar modo de treinador** para single **parâmetro,** utilize o módulo Modelo de [Comboio.](train-model.md)

    - Se definir **Criar o modo de treinador** para a gama de **parâmetros,** utilize o módulo [de hiperparametros do modelo de melodia.](tune-model-hyperparameters.md)

    > [!WARNING]
    > 
    > - Se passar uma gama de parâmetros para [o Modelo de Comboio,](train-model.md)utiliza apenas o primeiro valor na lista de parâmetros.
    > 
    > - Se passar um único conjunto de valores de parâmetros para o módulo [Tune Model Hyperparameters,](tune-model-hyperparameters.md) quando espera uma gama de definições para cada parâmetro, ignora os valores e utiliza os valores predefinidos para o aluno.
    > 
    > - Se selecionar a opção De Alcance de **Parâmetros** e introduzir um único valor para qualquer parâmetro, esse valor único especificado é utilizado ao longo da varredura, mesmo que outros parâmetros se alterem numa gama de valores.

11. Envie o oleoduto.

## <a name="results"></a>Results

Após o treino estar completo:

+ Para guardar uma imagem do modelo treinado, selecione o módulo de treino e, em seguida, mude para o **separador Outputs+logs** no painel direito. Clique no **conjunto de dados**do registo do ícone .  Pode encontrar o modelo guardado como um módulo na árvore do módulo.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.
