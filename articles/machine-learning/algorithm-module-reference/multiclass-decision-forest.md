---
title: 'Floresta de decisão multiclasse: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Multiclass Decision Forest em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo *da floresta de decisão.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477549"
---
# <a name="multiclass-decision-forest-module"></a>Módulo De Floresta de Decisão Multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo da *floresta de decisão.* Uma floresta de decisão é um modelo conjunto que rapidamente constrói uma série de árvores de decisão, enquanto aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre florestas de decisão

O algoritmo de decisão forest é um método de aprendizagem conjunto para classificação. O algoritmo funciona construindo múltiplas árvores de decisão e *votando* na classe de saída mais popular. Votar é uma forma de agregação, na qual cada árvore numa decisão de classificação produz um histograma de frequência não normalizado de rótulos. O processo de agregação resume estes histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. As árvores que têm alta confiança de previsão têm um maior peso na decisão final do conjunto.

As árvores de decisão em geral são modelos não paramétricos, o que significa que suportam dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó de folha (decisão) seja alcançado.

As árvores de decisão têm muitas vantagens:

+ Podem representar limites de decisão não lineares.
+ São eficientes na computação e utilização da memória durante o treino e a previsão.
+ Realizam seleção e classificação integradas de recursos.
+ São resistentes na presença de características ruidosas.

A decisão da escola florestal em Azure Machine Learning consiste num conjunto de árvores de decisão. Geralmente, os modelos conjuntos proporcionam uma melhor cobertura e precisão do que as árvores de decisão únicas. Para mais informações, consulte [A Decisão árvores.](https://go.microsoft.com/fwlink/?LinkId=403677)

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a Floresta de Decisão Multiclasse

1. Adicione o módulo **Multiclass Decision Forest** ao seu pipeline no designer. Pode encontrar este módulo em **Machine Learning,** **Modelo inicializar**e **Classificação.**

2. Clique duas vezes no módulo para abrir o painel **Properties.**

3. Para o **método de rerecolha,** escolha o método utilizado para criar as árvores individuais.  Pode escolher entre ensacar ou replicar.

    + **Bagging**: Bagging também é chamado de *agregação de botas.* Neste método, cada árvore é cultivada numa nova amostra, criada através da amostragem aleatória do conjunto de dados original com substituição até ter um conjunto de dados do tamanho do original. As saídas dos modelos são combinadas através do *voto*, que é uma forma de agregação. Para mais informações, consulte a entrada da Wikipedia para a gregação de Bootstrap.

    + **Replicação**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de que o predicado dividido é usado para cada nó de árvore permanece aleatória, criando árvores diversas.

   

4. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    + **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto de valores como argumentos.

    + **Gama de parâmetros**: Selecione esta opção se não tiver a certeza dos melhores parâmetros e pretenda fazer uma varredura de parâmetros. Selecione uma gama de valores para iterar mais, e o [Modelo tune Hyperparâmetros](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das definições que forneceu para determinar os hiperparâmetros que produzem os melhores resultados.   

5. **Número de árvores de decisão**: Digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, pode potencialmente obter uma melhor cobertura, mas o tempo de treino pode aumentar.

    Este valor também controla o número de árvores exibidas nos resultados, ao visualizar o modelo treinado. Para ver ou imprimir uma única árvore, pode definir o valor para 1; no entanto, isto significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros), e não são realizadas mais iterações.

6. **Profundidade máxima das árvores de decisão**: Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de alguma sobremontagem e aumento do tempo de treino.

7. **Número de divisões aleatórias por nó**: Digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

8. **Número mínimo de amostras por nó de folha**: Indicar o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore. Ao aumentar este valor, aumenta-se o limiar para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se aumentar o valor para 5, os dados de formação teriam de conter pelo menos cinco casos que satisfaçam as mesmas condições.



10. Ligue um conjunto de dados rotulado e um dos módulos de treino:

    + Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)

11. Submeta o oleoduto.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 