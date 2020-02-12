---
title: 'Floresta de decisão multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Multiclass Decision Forest em Azure Machine Learning para criar um modelo de aprendizagem automática baseado no algoritmo *da floresta de decisão.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 47cea412489693cedb05bd8a94a914b1757b8058
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152164"
---
# <a name="multiclass-decision-forest-module"></a>Módulo de floresta de decisão multiclasse

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para criar um modelo de aprendizagem automática baseado no algoritmo da *floresta de decisão.* Uma floresta de decisão é um modelo Ensemble que cria rapidamente uma série de árvores de decisão e, ao mesmo tempo, aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre as florestas de decisão

O algoritmo de floresta de decisão é um método de aprendizado Ensemble para classificação. O algoritmo funciona construindo múltiplas árvores de decisão e *votando* na classe de saída mais popular. A votação é uma forma de agregação, na qual cada árvore em uma floresta de decisão de classificação gera um histograma de frequência não normalizado de rótulos. O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" para cada rótulo. As árvores que têm alta confiança de previsão têm um peso maior na decisão final do Ensemble.

As árvores de decisão em geral são modelos não paramétricos, o que significa que dão suporte a dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja atingido.

As árvores de decisão têm muitas vantagens:

+ Eles podem representar limites de decisão não lineares.
+ Eles são eficientes na computação e no uso de memória durante o treinamento e a previsão.
+ Eles executam seleção e classificação de recursos integrados.
+ Eles são resilientes na presença de recursos ruidosas.

O classificador de floresta de decisão em Azure Machine Learning consiste em um Ensemble de árvores de decisão. Em geral, os modelos de Ensemble fornecem melhor cobertura e precisão do que árvores de decisão única. Para mais informações, consulte [A Decisão árvores.](https://go.microsoft.com/fwlink/?LinkId=403677)

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a floresta de decisão multiclasse

1. Adicione o módulo **Multiclass Decision Forest** ao seu pipeline no designer. Pode encontrar este módulo em **Machine Learning,** **Modelo inicializar**e **Classificação.**

2. Clique duas vezes no módulo para abrir o painel **Properties.**

3. Para o **método de rerecolha,** escolha o método utilizado para criar as árvores individuais.  Você pode escolher entre bagging ou replicação.

    + **Bagging**: Bagging também é chamado de *agregação de botas.* Nesse método, cada árvore é expandida em um novo exemplo, criada pela amostragem aleatória do conjunto de datas original com substituição, até que você tenha um conjunto de um DataSet com o tamanho original. As saídas dos modelos são combinadas através do *voto*, que é uma forma de agregação. Para obter mais informações, consulte a entrada da Wikipédia para agregação de bootstrap.

    + **Replicação**: Na replicação, cada árvore é treinada exatamente com os mesmos dados de entrada. A determinação de qual predicado de divisão é usado para cada nó de árvore permanece aleatório, criando árvores diversas.

   

4. Especifique como pretende que o modelo seja treinado, definindo a opção modo **Criar.**

    + **Parâmetro único**: Selecione esta opção se souber como pretende configurar o modelo e forneça um conjunto de valores como argumentos.


5. **Número de árvores de decisão**: Digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento pode aumentar.

    Esse valor também controla o número de árvores exibidas nos resultados, ao visualizar o modelo treinado. Para ver ou imprimir uma única árvore, você pode definir o valor como 1; no entanto, isso significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executada.

6. **Profundidade máxima das árvores de decisão**: Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum superajuste e um tempo de treinamento maior.

7. **Número de divisões aleatórias por nó**: Digite o número de divisões a utilizar ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

8. **Número mínimo de amostras por nó de folha**: Indicar o número mínimo de casos necessários para criar qualquer nó terminal (folha) numa árvore. Ao aumentar esse valor, você aumenta o limite para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.



10. Conecte um DataSet rotulado e um dos módulos de treinamento:

    + Se definir o modo Criar modo **de treinador** para um parâmetro **único,** utilize o módulo Modelo de [Comboio.](./train-model.md)

11. Executar o pipeline.



## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 